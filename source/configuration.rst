Configuration
=============

To configure REST API you need to edit the frontend configuration file
``app/config/frontend.ini.php``, for example

.. code-block:: php

    $config['api'] = array(
        'baseUrl' => '/api/v1',
        'allowedOrigins' => array(),
        'auth' => array(
            'component' => 'MyCustomAuth',
            'JWT' => array(
                'expiresIn' => 600,
                'alg' => 'HS256'
            ),
        ),
        'formatting' => array(
            'fields' => array(
                // fields that should be removed from results
                'remove' => array(
                    'title',
                    'Category' => array('name')
                ),
                // fields (removed by default) that should be kept
                'keep' => array(
                    'ip_created',
                    'Category' => array('object_type_id', 'priority')
                )
            )
        ),
        'validation' => array(
            'writableObjects' => array('document', 'event')
        )
    );

Possible configuration params are:

-  ``baseUrl`` the base url of REST API. Every request done to
   ``baseUrl`` will be handled as an API REST request via routing rules
-  ``allowedOrigins`` define which origins are allowed. Leave empty to
   allow all origins
-  ``auth`` contains authentication configurations:
-  ``component`` define the name of auth component to use. By default
   ``ApiAuth`` Component is used
-  ``JWT`` define some options used in `Json Web Token <http://jwt.io>`_ :doc:`authentication </endpoints/auth>`
   as the *"expires in"* time (in seconds) and the hashing algorithm to use
-  ``formatting`` permits to setup some fomatting rules as object fields to
   :ref:`remove <formatting-remove-fields>` or to :ref:`keep <formatting-keep-fields>`
-  ``validation`` setup some validation rules used generally in write
   operations. For example ``writableObjects`` define what object types
   are writable.
