Configuration
=============

Configuration parameters of a BEdita application are usually stored in PHP files and on a database table.

We have:

* ``config/app.php`` file containing App, Datasource, Cache, Log and other basic *low-level* settings common to every CakePHP application
* ``config`` table with BEdita specific parameters loaded after ``config/app.php``

To know more about configuration in CakePHP please read this `CakePHP book chapter <http://book.cakephp.org/3.0/en/development/configuration.html>`_

In ``config`` table every plugin (like API and Core BEdita plugins) can define and load its own parameters using its own `context` (have a look at **Accept** configuration for an example).

Using configurations keep in mind these common usage rules and best practices:

* ``config`` table records may generally override ``config/app.php``
* ``config`` table may not override *low-level* config settings like ``Datasources``, ``Cache``, ``EmailTransport``, ``Log``, ``Error`` and ``App``
* ``config/app.php`` configurations should follow `Twelve Factor app configuration principles <http://12factor.net/config>`_ : use environment variables for everything that is likely to vary between deploys

Below a brief BEdita configurations reference in alphabetical order.

.. _configuration-accept:

Accept
------

Define extra content types to accept in HTTP requests using ``Accept:`` header other than
*JSON* (``application/json``) and *JSONAPI* (``application/vnd.api+json``) that are always accepted.

Instead *HTML* (``text/html``, ``application/xhtml+xml``, ``application/xhtml``, ``text/xhtml``) generally not, but can be accepted through this configuration.
Same rule could be applied in the future to content types like *XML* and *YAML* (currently not supported).

In ``config/app.php`` we have

.. sourcecode:: php

    'Accept' => [
        'html' => filter_var(env('ACCEPT_HTML', 'false'), FILTER_VALIDATE_BOOLEAN),
    ]

In ``config`` table we may override it with a record with these fields

- *context*: ``'api'``
- *name*: ``'Accept'``
- *content*: ``{ "html" : true }``

.. _configuration-cors:

CORS
-----


It's possible to setup some basic `CORS <https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS>`_ configuration parameters directly in BEdita.

Using this settings please beware of possible conflicts with similar settings made on your HTTP server.

An optional 'CORS' configuration should be like this example:

.. sourcecode:: php

    'CORS' => [
        'allowOrigin' => '*.example.com', // string or array , also '*'
        'allowMethods' => ['GET', 'POST'], // (optional) array, also '*'
        'allowHeaders' => ['X-CSRF-Token'] // (optional) array, also '*'
    ]

Where:

- **CORS.allowOrigin** is a single domain or an array of domains
- **CORS.allowMethods** is an array of HTTP methods
- **CORS.allowHeaders** must be an array of HTTP headers

So if you want to allow every CORS call with the most permissive setting,
on development and test systems, you may set:

.. sourcecode:: php

    'CORS' => [
        'allowOrigin' => '*',  // allow every origin
        'allowMethods' => '*', // allow every method
        'allowHeaders' => '*'  // allow every header
    ]

.. _configuration-pagination:

Pagination
----------

Pagination settings for every API *list* response are done via ``'Pagination'`` key:

.. sourcecode:: php

    'Pagination' => [
        'limit' => 20,
        'maxLimit' => 100,
    ]

Where:

- **limit** - int - Default number of items per page as seen in `page_size` meta response and query string. Defaults to 20.
- **maxLimit** - int - Maximum acceptable items per page on a `page_size` request. Defaults to 100. This value cannot exceed 500.

Plugins
-------

Plugins setup for your BEdita instance is done through ``'Plugins'`` configuration key:

.. sourcecode:: php

    'Plugins' => [
        'DebugKit' => ['debugOnly' => true, 'bootstrap' => true],
        'MyPlugin' => ['autoload' => true, 'bootstrap' => true, 'routes' => true],
    ]


Where each key is a plugin name, and for each plugin available options are:

- **debugOnly** - boolean - (default: *false*) If true load this plugin in *'debug'* mode only.
- **bootstrap** - boolean - (default: *false*) If true load the ``$plugin/config/bootstrap.php`` file.
- **routes** - boolean - (default: *false*) If true load the ``$plugin/config/routes.php`` file.
- **ignoreMissing** - boolean - (default: *false*) If true ignore missing bootstrap/routes files.
- **autoload** - boolean - (default: *false*) Whether or not you want an autoloader registered

.. _configuration-security:

Security
--------

Additional security settings regarding anonymous access and :term:`JWT` (JSON Web Tokens) are possible, even though not mandatory.

.. sourcecode:: php

    'Security' => [
        //....
        'blockAnonymousApps' => true,
        'blockAnonymousUsers' => true,
        'jwt' => [
            'duration' => '+2 hours',
            'algorithm' => 'HS256',
        ],
    ],

Where:

- **Security.blockAnonymousUsers** when true on each request user must be identified, anonymous requests will receive a ``401 Unauthorized`` response - when false anonymous read requests (GET) are possible, but identification is always required for write operations (POST, PATCH, DELETE)
- **Security.blockAnonymousApps** when true on each request application must be identified, anonymous or unknown applications will receive a ``403 Forbidden`` response
- **Security.jwt.duration** is the default duration of the generated JWT. Keeping this value low increases security, but increases load on server as more renew requests will be performed by clients.
- **Security.jwt.algorithm** is the encryption algorithm used to issue new tokens. Must be one of ``HS256``, ``HS512``, ``HS384``, or ``RS256``.
