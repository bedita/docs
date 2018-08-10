Folders ``/folders``
====================

``/folders`` endpoint provide folders management: search, create, modify, etc.

Mainly it provides:
    * create a folder
    * get a single folder
    * get a collection of folders, using filters like trees navigation, search, types, partent
    * modify or remove an existing folder
    * get or set a parent folder
    * add or remove contents
    * add or remove subfolders



Create a folder
---------------

You can create folders by using ``POST /folders`` endpoint.
Folders data must be specified inside body JSON data.

Request body structure is:

.. sourcecode:: json

    {
        "data": {
            "type": "folders",
            "attributes": {}
        }
    }

.. http:post:: /folders

**Example request (create sample folder)**:

.. sourcecode:: http

    POST /folders HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "folders",
            "attributes": {
                "title": "Root Folder"
            }
        }
    }

Expected response is ``HTTP/1.1 201 Created``, with ``application/vnd.api+json`` body data representing the folder just created.

When folder already exists or data is not valid (i.e. data lacks of required fields), POST fails and response is ``400 Bad Request - Invalid data``.

Successful response example follows:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "5593",
            "type": "folders",
            "attributes": {
                "uname": "root-folder",
                "title": "Root Folder"
            },
            "meta": {
                "created": "2018-08-10T10:19:00+00:00",
                "modified": "2018-08-10T10:19:00+00:00",
                "path": "/5593"
            },
            "relationships": {
                "children": {
                    "links": {
                        "related": "https://example.com/folders/5593/children",
                        "self": "https://example.com/folders/5593/relationships/children"
                    }
                },
                "parent": {
                    "links": {
                        "related": "https://example.com/folders/5593/parent",
                        "self": "https://example.com/folders/5593/relationships/parent"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/folders",
            "home": "https://example.com/home"
        },
        "meta": {
            "schema": {
                "folders": {
                    "$id": "https://example.com/model/schema/folders",
                    "revision": "1169288309"
                }
            }
        }
    }

Get a folder
------------

.. http:get:: /folders/(folder_id)

Retrieve folder details.

    **Example request**:

    .. sourcecode:: http

        GET /folders/5593 HTTP/1.1
        Host: example.com
        Accept: application/vnd.api+json

    **Example response**:

    .. sourcecode:: http

        HTTP/1.1 200 OK
        Content-Type: application/json

        {
            "data": {
                "id": "5593",
                "type": "folders",
                "attributes": {
                    "title": "Root Folder"
                },
                "meta": {
                    "created": "2018-08-10T10:19:00+00:00",
                    "modified": "2018-08-10T10:19:00+00:00",
                    "path": "/5593"
                },
                "relationships": {
                    "children": {
                        "links": {
                            "related": "https://example.com/folders/5593/children",
                            "self": "https://example.com/folders/5593/relationships/children"
                        }
                    },
                    "parent": {
                        "links": {
                            "related": "https://example.com/folders/5593/parent",
                            "self": "https://example.com/folders/5593/relationships/parent"
                        }
                    }
                }
            },
            "links": {
                "self": "https://example.com/folders/5593",
                "home": "https://example.com/home"
            },
            "meta": {
                "schema": {
                    "folders": {
                        "$id": "https://example.com/model/schema/folders",
                        "revision": "1169288309"
                    }
                }
            }
        }
