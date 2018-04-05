Get a collection of objects
===========================

The ``/objects`` endpoint can be used to retrieve a collection of objects.

.. http:get:: /objects

    It returns a collection of objects:

    * if called with ``id`` query string parameter the response will contain a collection of the objects requested
    * else it returns a paginated list of objects that are descendants of the
      related publication configured in ``app/config/frontend.ini.php``.

    **Example request**:

    .. sourcecode:: http

        GET /objects HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    For readability the fields of objects are limited to "title" but they are similar to :http:get:`/objects/(object_id)` example

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "data": [
                {
                    "id": 1,
                    "type": "documents", // just as example, documents
                    "attributes": { // the object attributes
                        ...
                    },
                    "meta": { // the object meta data
                        ...
                    },
                    "links": {
                        ...
                    },
                    "relationships": {
                        ...
                    }
                },
                ...
            ],
            "links": {
                ...
            },
            "meta": {
                "pagination": { // just example data
                    "count": 14336,
                    "page": 1,
                    "page_count": 717,
                    "page_items": 20,
                    "page_size": 20
                },
                "schema": {
                    ...
                }
            }
        }
