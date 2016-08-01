Configuration
=============


Configuration parameters of a BEdita4 application are usually stored in PHP files and on a database table.

We have:

* ``config/app.php`` file containing App, Datasource, Cache, Log and other basic *low-level* settings common to every CakePHP application
* ``config`` table with BEdita4 specific parameters loaded after ``config/app.php``

To know more about configuration in CakePHP please read this `CakePHP book chapter <http://book.cakephp.org/3.0/en/development/configuration.html>`_

In ``config`` table every plugin (like API and Core BEdita4 plugins) can define and load its own parameters using its own `context` (have a look at **Accept** configuration for an example).

Using configurations keep in mind these common usage rules and best practices:

* ``config`` table records may generally override ``config/app.php``
* ``config`` table may not override *low-level* config settings like ``Datasources``, ``Cache``, ``EmailTransport``, ``Log``, ``Error`` and ``App``
* ``config/app.php`` configurations should follow `Twelve Factor app configuration principles <http://12factor.net/config>`_ : use environment variables for everything that is likely to vary between deploys

Below a brief BEdita4 configurations reference in alphabetical order.


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


ApiKeys
-------

*Api Keys* are stored in configuration with this structure:

.. sourcecode:: php

    'ApiKeys' => [
        // array key is the actual api key string
        'sdgwr89081023jfdklewRASdasdwdfswdr' => [
            'label' => 'web app', // (optional)
            'origin' => 'example.com', // (optional) could be '*'
        ],
        'w4nvwpq5028DDfwnrK2933293423nfnaa4' => [
            ....
         ],
    ]

Api Keys are checked on every request using this rules:

* if no Api Keys are defined HTTP request is always accepted
* if one or more Api Keys are defined:

    - current ``X-Api-Key`` header value is read should match one of these keys
    - if corresponding Api Key has an `origin` - HTTP request `origin` is read and should also match
    - otherwise an error response is sent using HTTP 403 code


CORS
-----


It's possible to setup some basic `CORS <https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS>`_ configuration parameters directly in BEdita4.

Using this settings please beware of possible conflicts with similar settings made on your HTTP server.

An optional 'CORS' configuration should be like this example:

.. sourcecode:: php

    'CORS' => [
        'allowOrigin' => '*.example.com', // string or array
        'allowMethods' => ['GET', 'POST'], // (optional) array
        'allowHeaders' => ['X-CSRF-Token'] // (optional) array
    ]

Where:

- **CORS.allowOrigin** is a single domain or an array of domains
- **CORS.allowMethods** is an array of HTTP methods
- **CORS.allowHeaders** must be an array of HTTP headers

Security
--------

Additional security settings regarding JWT (JSON Web Tokens) are possible, even though not mandatory.

.. sourcecode:: php

    'Security' => [
        // ...
        'jwt' => [
            'duration' => '+2 hours',
            'algorithm' => 'HS256',
        ],
    ],

Where:

- **Security.jwt.duration** is the default duration of the generated JWT. Keeping this value low increases security, but increases load on server as more renew requests will be performed by clients.
- **Security.jwt.algorithm** is the encryption algorithm used to issue new tokens. Must be one of ``HS256``, ``HS512``, ``HS384``, or ``RS256``.
