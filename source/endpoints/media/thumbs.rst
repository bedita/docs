Media thumbs ``/media/thumbs``
==============================

``/media/thumbs`` base endpoint provides a set of methods dedicated to thumbnails generation and retrieval.

Thumbnails can be requested by the client using the ``GET /media/thumbs/:id`` or ``GET /media/thumbs?ids=:comma-separated-ids`` endpoints,
where `:id` is a media identifier, `:ids` is a string containing media identifiers (separated by a comma: ``ids=1,2,3``).

A preset can be specified using the ``?preset=<preset-name>`` query parameter,
or (if explicitly allowed via configuration) a whole set of options — width, height, format, etc.

**Example request**:

.. sourcecode:: http

    GET /media/thumbs?ids=1,2,3&preset=async HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        // ...
        "meta": {
            "thumbnails": [
                {
                    "id": 1,
                    "uuid": "some-uuid-1",
                    "ready": false,
                    "url": "http://example.com/something.jpg"
                },
                {
                    "id": 2,
                    "uuid": "some-uuid-2",
                    "ready": true,
                    "url": "http://example.com/something-else.jpg"
                },
                {
                    "id": 3,
                    "uuid": "some-uuid-3",
                    "ready": false,
                    "acceptable": false,
                    "url": "http://example.com/something.txt"
                }
            ]
        }
    }

The `ready` response value tells you if the thumbnail has been already generated and it's already available at `url` response value.
The `acceptable` response value is `false` when image thumb cannot be created.

Thumbnails, set of options — width, height
------------------------------------------

You can request a thumbnail with specified options (width, height), using options in query string, as in following examples:

.. sourcecode:: http

    GET /media/thumbs/{{id}}?options[w]=100 HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs/{{id}}?options[h]=100 HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs/{{id}}?options[w]=100&options[h]=100 HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

Thumbnails by preset
--------------------

Presets allow you to use specific options and thumbnails generator (synchronous or asynchronous),
according to the BEdita API instance configuration.

In the following example, BEdita api config provides various presets:

 - `default` (with synchronous *Glide* generator, width 800, height 600),
 - `small` (with synchronous *Glide* generator, width 100),
 - `medium` (with synchronous *Glide* generator, width 400),
 - `async` (with asynchronous *Async* generator, width 800, height 600).

.. sourcecode:: php

    'Thumbnails' => [
        'allowAny' => filter_var(env('THUMBNAILS_ALLOW_ANY', false), FILTER_VALIDATE_BOOLEAN),
        'presets' => [
            'default' => [
                'w' => 800,
                'h' => 600,
            ],
            'small' => [
                'w' => 100,
            ],
            'medium' => [
                'w' => 400,
            ],
            'async' => [
                'generator' => 'async',
                'w' => 800,
                'h' => 600,
            ],
        ],
        'generators' => [
            'default' => [
                'className' => 'BEdita/Core.Glide',
                'url' => env('THUMBNAILS_DEFAULT_URL', null),
            ],
            'async' => [
                'className' => 'BEdita/Core.Async',
                'url' => env('THUMBNAILS_ASYNC_URL', null),
            ],
        ],
    ],

In this example, you could use the presets as follows:

.. sourcecode:: http

    GET /media/thumbs/{{id}}?preset=small HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs?ids=1,2,3&preset=small HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs/{{id}}?preset=medium HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs?ids=1,2,3&preset=medium HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs/{{id}}?preset=async HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

.. sourcecode:: http

    GET /media/thumbs?ids=1,2,3&preset=async HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
