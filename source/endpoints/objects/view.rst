Get single object
=================

.. http:get:: /objects/(object_id)

    Get an object detail.

    **Example request**:

    .. sourcecode:: http

        GET /objects/15 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "data": {
                "id": 1,
                "type": "documents", // just as example, documents
                "attributes": {
                    ...
                },
                "meta": {
                    ...
                },
                "links": {
                    ...
                },
                "relationships": {
                    ...
                }
            },
            "links": {
                ...
            },
            "meta": {
                "schema": {
                    "documents": {
                        "$id": "http://example.com/model/schema/documents",
                        "revision": "2716038208"
                    }
                }
            }
        }
