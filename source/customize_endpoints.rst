Customize endpoints
===================

.. _custom-endpoints:

Custom endpoints
----------------

Once you have :doc:`enabled a frontend to consume API </setup>`
you have a set of :doc:`default available endpoints </endpoints/index>`
visible pointing the browser to your API base url.

Sometimes you would want to define other endpoints to serve your custom
data to clients. You can do it simply override the ``$endpoints``
attribute of ``ApiBaseController``.

Write in your ``ApiController``

.. code-block:: php

    protected $endPoints = array('friends');

and define the related custom method that will handle the data to show

.. code-block:: php

    protected function friends() {
        $friendsList = array('Tom', 'Jerry');
        $this->setData($friendsList);
    }

The ``setData()`` method takes care of put ``$friendsList`` array inside
response ``data`` key. Point the browser to your API base url you should
see 'friends' in the endpoints list and if you request
``GET /api/base/url/friends`` you should see

.. code-block:: json

    {
        "api": "friends",
        "data": [
            "Tom",
            "Jerry"
        ],
        "method": "get",
        "params": [],
        "url": "https://example.com/api/v1/friends"
    }

In this way all request types (GET, POST, PUT, DELETE) have to be
handled by ``friends()`` method. Another possibility is to create one
method for every request type allowed from the endpoint. It can be done
creating methods named *"request type + endpoint camelized"*.

.. code-block:: php

    protected function getFriends() {
    }

    protected function postFriends() {
    }

    protected function putFriends() {
    }

    protected function deleteFriends() {
    }


Blacklist endpoints
-------------------

In some situations you will not want to expose some or all default
endpoints, so in order to disable them it is possible to define a
blacklist. After that calling those endpoints the response will be a
**405 Method Not Allowed** HTTP error status code.

For example to blacklist "auth" and "objects" endpoints, in your
``ApiController`` override ``$blacklistEndPoints`` writing

.. code-block:: php

    protected $blacklistEndPoints = array('auth', 'objects');

Now, pointing to API base url you shouldn't see "auth" and "objects" endpoints anymore.

Pointing to them directly and you will receive a **405 HTTP error**.


Enable special object types endpoints
-------------------------------------

If you need you can also enable some special endpoint disabled by
default. Those endpoints refer to BEdita object types mapping them to
their pluralize form. So if you want to enable ``/documents`` end
``/galleries`` endpoints you have to edit ``ApiController``

.. code-block:: php

    protected $whitelistObjectTypes = array('document', 'gallery');

These special endpoints automatically filter response objects through
the object type related.

Again go to API base url to see 'documents' and 'galleries' added to
endpoints list.

   .. note::

      Note that those special endpoints work only for GET requests.

.. _setup-query-parameters:

Setup query string parameters
-----------------------------

You can easily customize which `query string parameters <https://en.wikipedia.org/wiki/Query_string>`_ an endpoint can accept.
By deafult every endpoint accepts the :term:`access_token <access token>` query string (also if it is
suggested to pass it in HTTP header Authorization). That is also valid for custom endpoints
you create.

Moreover :doc:`default endpoints </endpoints/index>` support additional query string params
according to ``ApiBaseController::$defaultAllowedUrlParams``.

Every time a request is fullfilled with query strings parameters they are validated against
those allowed. If validation fails the response return a :http:statuscode:`400` status code.

To validate your own query string parameters there are two ways. Directly in ``ApiController`` or via configuration.

Configure allowed query string parameter in ``ApiController``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To validate your own query string parameters you can define the ``$allowedUrlParams``
in ``ApiController`` as

.. code-block:: php

    protected $allowedUrlParams = array(
        'endpoint_name' => array('param_one', 'param_two')
    );

Then you can send request as

.. sourcecode:: http

    GET /endpoint_name?param_one=value HTTP/1.1

without receive a :http:statuscode:`400` error.

To group some parameters that you want to make availbale to more endpoints, the ``_`` prefix can be used
in the array keys. It will be considered as a special word used as *group name* instead of *endpoint name*.

.. code-block:: php

    protected $allowedUrlParams = array(
        // define a group of params
        '_paramGroup' => array('param_one', 'param_two'),
        // use group in endpoints
        'endpoint_one' => array('_paramGroup'),
        'endpoint_two' => array('_paramGroup', 'param_three')
    );

Configure allowed query string parameter in configuration file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Using the same convention seen above you can customize the allowed query string editing ``app/config/frontend.ini.php``
or ``app/config/frontend.cfg.php``

.. code-block:: php

    $config['api'] = array(
        'baseUrl' => '/api',
        // other conf params
        // ...
        'validation' => array(
            'allowedUrlParams' => array(
                // define a group of params
                '_paramGroup' => array('param_one', 'param_two'),
                // use group in endpoints
                'endpoint_one' => array('_paramGroup'),
                'endpoint_two' => array('_paramGroup', 'param_three')
            )
        )
    );

Add allowed query string parameters on the fly
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you need to change the allowed parameters on the fly ``ApiValidator`` provides
a method to register them.

.. code-block:: php

    // In ApiController
    $allowedParams = array('objects' => 'custom_param');
    // add "custom_param" to "objects" endpoint allowed params
    $this->ApiValidator->registerAllowedUrlParams($allowedParams);

    // or if you want override all rules with new one
    $this->ApiValidator->registerAllowedUrlParams($allowedParams, false);


Customize ``/objects`` endpoint
-------------------------------

Here we'll see how to customize the :doc:`objects </endpoints/objects>` endpoint.

Configure your own URL path filter types
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:doc:`objects </endpoints/objects>` endpoint can be customized with URL path filters building
endpoint structured as ``/objects/:id/url_path_filter``. URL path
filters on by default are visible in ``ApiBaseController::$allowedObjectsUrlPath`` property

.. code-block:: php

    protected $allowedObjectsUrlPath = array(
            'get' => array(
                'relations',
                'children',
                'contents',
                'sections',
                'descendants',
                'siblings',
                //'ancestors',
                //'parents'
            ),
            'post' => array(
                'relations',
                'children'
            ),
            'put' => array(
                'relations',
                'children'
            ),
            'delete' => array(
                'relations',
                'children'
            )
        );

URL path filters can be inhibited or new ones can be added overriding
that property in ``ApiController``.

In practice URL path filters are divided by request type (GET, POST,
...) so it is possible doing request like ``GET /objects/1/children``,
``POST /objects/1/relations`` but not ``POST /objects/1/siblings``
because of that filter is active only for GET requests.

Every URL path filter must have a corresponding controller method named
*"request type + Objects + URL path filter camelized"* that will handle
the request. First url part *:id* and every other url parts after URL
path filter will be passed to that method as arguments.

For example, supposing to want to remove all 'delete' and 'post' URL
path filters and add a new 'foo\_bar' filter for GET request, in
``ApiController`` we can override

.. code-block:: php

    protected $allowedObjectsUrlPath = array(
            'get' => array(
                'relations',
                'children',
                'contents',
                'sections',
                'descendants',
                'siblings',
                'foo_bar'
            ),
        );

and add the method

.. code-block:: php

    protected function getObjectsFooBar($objectId) {
        // handle request here
    }

In this way the new URL path filter is active and reachable from
``GET /objects/:id/foo_bar``. Every other request type (POST, PUT,
DELETE) to that will receive **405 Method Not Allowed**.

If our 'foo\_bar' URL path filter have to support
``GET /objects/:id/foo_bar/:foo_val`` requests then
``ApiController::getObjectsFooBar()`` will receive ``:foo_val`` as
second argument. A best practice should be to add to method a validation
on the number of arguments supported to avoid to respond to request as
``GET /objects/:id/foo_bar/:foo_val/bla/bla/bla``.

.. code-block:: php

    protected function getObjectsFooBar($objectId, $fooVal = null) {
        if (func_num_args() > 2) {
            throw new BeditaBadRequestException();
        }
        // handle request here
    }

Configure query string paramters to filter objects
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:ref:`Previoulsy <setup-query-parameters>` we have seen how to add custom allowed
query string params to endpoints.

The default allowed params are visible in :http:get:`/objects`.
In particular a special query string parameter is used to filter objects, its name is ``filter[]``
and it's an array of conditions to apply to get collections of objects.

For example

.. sourcecode:: http

    GET /objects?filter[object_type]=document,gallery,event HTTP/1.1

will return a collection of publication's descendants of type *document* or *gallery* or *event*.
Filters are chained so you can do

.. sourcecode:: http

    GET /objects?filter[object_type]=document,gallery&filter[query]=test HTTP/1.1

to obtain a collection of publication's descendants of type *document* or *gallery*
containing the word "test" in some of their indexed fields.

In general you can define other useful filters with this convention

::

    filter[objects_table_field_name]

where "objects_table_field_name" is a field of the objects table.

Or

::

    filter[Model.field_name]

if you want to filter on a field in another `table that extends objects table <http://docs.bedita.com/bedita-objects-database-tables>`_.
For example if you want to filter by *name* field in *cards* table
we would configure the allowed param ``filter[Card.name]``

.. code-block:: php

    // frontend.ini.php
    $config['api'] = array(
        'baseUrl' => '/api',
        // other conf params
        // ...
        'validation' => array(
            'allowedUrlParams' => array(
                'objects' => array('filter[Card.name]')
            )
        )
    );

then we can search all publication's descendants of type *card* with *name* equal to "Tom" or "Jerry".

.. sourcecode:: http

    GET /objects?filter[object_type]=card&filter[Card.name]=Tom,Jerry HTTP/1.1
