Trash ``/trash``
================

``/trash`` endpoint deals with trashcan contents management.

Main responsabilities of this endpoint:
    * show trashcan contents
    * view trashcan single content
    * restore object (remove from trashcan, restore to system)
    * delete object (remove from database)

Contents in trashcan
--------------------

You can obtain trash contents by using ``GET /trash`` and ``GET /trash/(object_id)`` endpoint.

.. http:get:: /trash/

``GET /trash`` returns response ``200 OK`` and contents as array, in 'data', as described in following example.

    **Example request**:

    .. sourcecode:: http

        GET /trash HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "data": [
              ....
            ],
            ....
        }

.. http:get:: /trash/(object_id)

``GET /trash/(object_id)`` returns response ``200 OK`` if content is found, ``404 Not Found`` otherwise.

    **Example request**:

    .. sourcecode:: http

        GET /trash/154 HTTP/1.1
        Host: example.com
        Accept: application/json, text/javascript

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "data": {
              ....
            },
            ....
        }

Restore contents
----------------

You can restore contents by using ``PATCH /trash/(object_id)`` endpoint.

.. http:patch:: /trash/(object_id)

**Example request (restore object 55920)**

In this example, purpose is restoring object 55920.

.. sourcecode:: http

    PATCH /trash/55920 HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 55920,
            "type": "objects"
        }
    }

Response ``204 No Content`` is expected. When object is not found in trashcan, response is ``404 Not Found``.

.. sourcecode:: http

    HTTP/1.1 204 No Content
    Content-Type: application/vnd.api+json


Delete contents
---------------

You can completely remove contents from system by using ``DELETE /trash/(object_id)`` endpoint.

.. http:delete:: /trash/(object_id)

**Example request (delete object 55920)**:

.. sourcecode:: http

    DELETE /trash/55920 HTTP/1.1
    Host: example.com

Expected response is ``204 No Content``. When object is not found in trashcan, response is ``404 Not Found``.

.. sourcecode:: http

    HTTP/1.1 204 No Content
