Formatting BEdita objects
=========================

Introducing the ``ApiFormatter`` Component
------------------------------------------

To respond with consistent data the BEdita object types are transformed
and formatted using the ``ApiFormatter`` Component that deals with
cleaning objects from useless data and casting and trasforming some
fields in correct format.

If you have a look at ``/objects/:id`` response you'll see that fields
as 'id' are **integer** other like 'latitude' and 'longitude' of geo tag
are **float** and **dates are formatted in ISO-8601**. ``ApiFormatter``
Component with a little help from Models takes care of it.

When you load an object or list of objects you should always use the
``ApiFromatter`` Component to have data always formatted in the same
way.

.. code-block:: php

    // load an object
    $object = $this->loadObj($id);
    $result = $this->ApiFormatter->formatObject($object); 
    // in $result['object'] you have the formatted object

    // list of objects
    $objects = $this->loadSectionObjects($id, array('itemsTogether' => true));
    $result = $this->ApiFormatter->formatObjects($objects['children']);
    // in $result['objects'] you have the formatted objects

``ApiFormatter::formatObject()`` and ``ApiFormatter::formatObjects()``
accept as second argument an array of options with which it is possible
add to the formatted object the count of relations and children.

.. code-block:: php

    $result = $this->ApiFormatter->formatObject($object, array(
        'countRelations' => true,
        'countChildren' => true
    ));

By default no count is done.


Help ApiFormatter to cast object fields in the right way
--------------------------------------------------------

When formatting BEdita object ``ApiFormatter`` asks help to related
object type Model to know which fields have to be cast in the right
type. Basically every object type returns an array of fields that are
defined in database as 'integer', 'float', 'date', 'datetime',
'boolean'. This array is returned from
``BEAppObjectModel::apiTransformer()`` method and it is something
similar to

.. code-block:: php

    array(
        'id' => 'integer',
        'start_date' => 'datetime',
        'end_date' => 'datetime',
        'duration' => 'integer',
        'object_type_id' => 'integer',
        'created' => 'datetime',
        'modified' => 'datetime',
        'valid' => 'boolean',
        'user_created' => 'integer',
        'user_modified' => 'integer',
        'fixed' => 'boolean',
        'GeoTag' => array(
            'id' => 'integer',
            'object_id' => 'integer',
            'latitude' => 'float',
            'longitude' => 'float',
            'gmaps_lookat' => array(
                'latitude' => 'float',
                'longitude' => 'float',
                'zoom' => 'integer',
            )

        )
        'Tag' => array(
            'id' => 'integer',
            'area_id' => 'integer',
            'object_type_id' => 'integer',
            'priority' => 'integer',
            'parent_id' => 'integer',
        ),
        'Category' => array(
            'id' => 'integer',
            'area_id' => 'integer',
            'object_type_id' => 'integer',
            'priority' => 'integer',
            'parent_id' => 'integer',
        )
    )

By default only tables that form the object chain plus 'categories',
'tags' and 'geo\_tags' are automatically returned, but that method can
be overridden to customize the result. For example the ``Event`` model
add to basic transformer the ``DateItem`` transformer:

.. code-block:: php

    public function apiTransformer(array $options = array()) {
        $transformer = parent::apiTransformer($options);
        $transformer['DateItem'] = $this->DateItem->apiTransformer($options);
        return $transformer;
    }

The ``ApiFormatter`` uses these transformers merged to common object
transformer ``ApiFormatterComponent::$transformers['object']`` to present
consistent data to client. It is possible to use some special
transformer types that are:

-  ``underscoreField`` that underscorize a camelcase field maintaining
   value unchanged
-  ``integerArray`` that cast to integer all array values


.. _formatting-remove-fields:

Remove unwanted fields
----------------------

Another useful task of ``ApiFormatter`` is to clean unwanted fields from
data exposed to client. To do that it uses
``ApiFormatter::$objectFieldsToRemove`` array that can be customized
through configuration or on the fly in controller.

Add fields to remove from configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In ``config/frontend.ini.php`` or ``config/frontend.cfg.php`` is
possible to customize which fields exposed by default you want to remove
from results.

.. code-block:: php

    $config['api'] = array(
        'baseUrl' => '/api/v1',
        ...
        'formatting' => array(
            'fields' => array(
                // fields that should be added
                // to ApiFormattingComponent::objectFieldsToRemove
                // i.e. removed from formatted object
                'remove' => array(
                    'description',
                    'title',
                    'Category' => array('name'),
                    'GeoTag' => array('title'),
                    'Tag'
                )
            )
        )
    );

In this way you say to ``ApiFormatter`` that 'description', 'title',
'name' of 'Category', 'title' of 'GeoTag' and all 'Tag' array must be
cleaned from final results. Every time ``ApiFormatter::formatObject()``
or ``ApiFormatter::formatObjects()`` is called the data are cleaned up
using ``ApiFormatter::cleanObject()``.

Add fields to remove on the fly
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In your ``ApiController`` you can decide in every moment to change which
fields remove from results using
``ApiFormatter::objectFieldsToRemove()`` method.

.. code-block:: php

    // get the current value
    $currentFieldsToRemove = $this->ApiFormatter->objectFieldsToRemove();

    // to ovveride all. It completely replaces current fields to remove with new one
    $this->ApiFormatter->objectFieldsToRemove(
        array(
            'title',
            'description'
        ),
        true
    );

    // to add new fields to remove
    $this->ApiFormatter->objectFieldsToRemove(array(
        'remove' => array('title', 'description')
    ));


.. _formatting-keep-fields:

Keep fields that are removed by default
---------------------------------------

Sometime you could want to present to client some fields that normally
are cleaned up. Likewise to what seen with fields to remove, it is
possible do it from configuration or on the fly.

Add fields to keep from configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In ``config/frontend.cfg.php``

.. code-block:: php

    $config['api'] = array(
        'baseUrl' => '/api/v1',
        ...
        'formatting' => array(
            'fields' => array(
                // fields that should be removed
                // to ApiFormattingComponent::objectFieldsToRemove
                // i.e. kept in formatted object
                'keep' => array(
                    'fixed',
                    'ip_created',
                    'Category' => array('object_type_id', 'priority')
                )
            )
        )
    );

In this way you say to ``ApiFormatter`` that 'fixed', 'ip\_created' and
'object\_type\_id', 'priority' of 'Category' must be preserved and
presented to client.

Add fields to keep on the fly
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In your ``ApiController``

.. code-block:: php

    // to keep fields
    $this->ApiFormatter->objectFieldsToRemove(array(
        'keep' => array('ip_created', 'fixed')
    ));

It is possible to mix 'remove' and 'keep' options both in configuration
and in controller.
