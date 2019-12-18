Objects ``/objects``
====================

``/objects`` endpoint deals mainly with object retrieval and CRUD operations on objects in general.

Main responsabilities of this endpoint are:
    * get a single object
    * get a collection of objects, using many filters like trees navigation, search, types
    * create a new object
    * modify or remove an existing object

Get a single object
-------------------

.. http:get:: /objects/(object_id)

    Get an object detail.

    **Example request**:

    .. sourcecode:: http

        GET /objects/15 HTTP/2
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/2 200 OK
        Content-Type: application/json

        {
            "api": "objects",
            "data": {
              ....
            },
            "method": "get",
            "params": [],
            "url": "https://example.com/api/objects/15"
        }



Get a collection of objects
---------------------------

The ``/objects`` endpoint can be used to retrieve a collection of objects.

.. http:get:: /objects

    It returns a collection of objects:

    * if called with ``id`` query string parameter the response will contain a collection of the objects requested
    * else it returns a paginated list of objects that are descendants of the
      related publication configured in ``app/config/frontend.ini.php``.

    **Example request**:

    .. sourcecode:: http

        GET /objects HTTP/2
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    For readability the fields of objects are limited to "title" but they are similar to :http:get:`/objects/(object_id)` example

    .. sourcecode:: http

        HTTP/2 200 OK
        Content-Type: application/json

        {
            "api": "objects",
            "data": {
                "objects": [
                    {
                        "id": 2,
                        "title": "title one"
                    },
                    {
                        "id": 3,
                        "title": "title two"
                    },
                    {
                        "id": 4,
                        "title": "title three"
                    },
                    {
                        "id": 5,
                        "title": "title four"
                    },
                    {
                        "id": 6,
                        "title": "title five"
                    }
                ]
            },
            "method": "get",
            "paging": {
                "page": 1,
                "page_size": 5,
                "page_count": 5,
                "total": 50,
                "total_pages": 10
            },
            "params": [],
            "url": "https://example.com/api/objects/1/children"
        }
