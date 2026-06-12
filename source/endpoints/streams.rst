Streams ``/streams``
====================

``/streams`` endpoint provides file upload, CRUD operations on streams and association of media (i.e. image) to a :term:`stream`.

Main responsabilities of this endpoint are:

* file upload and new :term:`stream` creation
* get a collection of streams, using usual :ref:`get-filters`
* link a :term:`stream` to a media object
* remove an existing stream
* upload a new version of an existing stream
* replace the latest version of an existing stream in place

Upload file and create stream
-----------------------------

.. http:post:: /streams/upload/{file_name}

    Upload a file, passing it as binary in `body`.

**Example request**:

.. sourcecode:: http

    POST /streams/upload/myfile.jpg HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: image/jpeg

**Example response**:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/json

    {
        "data": {
            "id": "991c27b0-9e3a-43ca-a9dd-6c1f831eca31",
            "type": "streams",
            "attributes": {
                "file_name": "myfile.jpg",
                "mime_type": "image/png"
            },
            "meta": {
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "http://example.com/streams/991c27b0-9e3a-43ca-a9dd-6c1f831eca31/object",
                        "self": "http://example.com/streams/991c27b0-9e3a-43ca-a9dd-6c1f831eca31/relationships/object"
                    }
                }
            },
            "links": {
                "self": "http://example.com/streams/upload/myfile.jpg",
                "home": "http://example.com/home"
            },
            "meta": {
                "schema": {
                    "streams": {
                        "$id": "http://example.com/model/schema/streams",
                        "revision": "627017864"
                    }
                }
            }
        }
    }

Get a single stream
-------------------

.. http:get:: /streams/(stream_id)

    Get data for a stream by id.

**Example request**:

.. sourcecode:: http

    GET /streams/302f6fdd-bf31-4cd7-91db-baf45fb93de0 HTTP/1.1
    Host: example.com

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "302f6fdd-bf31-4cd7-91db-baf45fb93de0",
            "type": "streams",
            "attributes": {
                "file_name": "myfile.jpg",
                "mime_type": "image/png"
            },
            "meta": {
                "...": "..."
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "http://example.com/streams/302f6fdd-bf31-4cd7-91db-baf45fb93de0/object",
                        "self": "http://example.com/streams/302f6fdd-bf31-4cd7-91db-baf45fb93de0/relationships/object"
                    }
                }
            }
        },
        "links": {
            "..." : "..."
        },
        "meta": {
            "..." : "..."
        }
    }

Get a collection of streams
---------------------------

The ``/streams`` endpoint retrieve a collection of streams.

.. http:get:: /streams

    It returns a collection of streams:

    * if called with ``id`` query string parameter the response will contain a collection of the streams requested
    * else it returns a paginated list of streams that are descendants of the
      related publication configured in ``app/config/frontend.ini.php``.

**Example request**:

.. sourcecode:: http

    GET /streams HTTP/1.1
    Host: example.com

**Example response**:

    For readability the fields of objects are limited to "title" but they are similar to :http:get:`/streams/(stream_id)` example

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "0dae7797-5cc8-4d63-8a11-98664dd675b0",
                "type": "streams",
                    "attributes": {
                        "file_name": "myfile.png",
                        "mime_type": "image/png"
                    },
                    "meta": {
                        "..." : "..."
                    },
                    "links": {
                        "..." : "..."
                    },
                    "relationships": {
                        "..." : "..."
                    }
            }
        ],
        "links": {
            "..." : "..."
        },
        "meta": {
            "..." : "..."
        },
    }

Create an image
---------------

``POST /images`` provides image creation.

.. http:post:: /images

**Example request**:

.. sourcecode:: http

    POST /images HTTP/1.1
    Host: example.com
    Content-Type:application/vnd.api+json
    Accept: application/vnd.api+json

**Example body**:

.. sourcecode:: json

    {
        "data": {
            "type": "images",
            "attributes": {
                "title": "My media"
            }
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "6",
            "type": "images",
            "attributes": {
                        "..." : "..."
            },
            "meta": {
                        "..." : "..."
            },
            "relationships": {
                        "..." : "..."
            }
        },
        "links": {
            "..." : "..."
        },
        "meta": {
            "..." : "..."
        }
    }

Link stream to media
--------------------

Link a stream to a media using ``PATCH /streams/{stream_uuid}/relationships/object``.

.. http:patch:: /streams/{stream_uuid}/relationships/object

**Example request**:

.. sourcecode:: http

    PATCH /streams/302f6fdd-bf31-4cd7-91db-baf45fb93de0/relationships/object HTTP/1.1
    Host: example.com
    Content-Type: application/vnd.api+json
    Accept: application/vnd.api+json

**Example body**:

.. sourcecode:: json

    {
        "data": {
            "id": "6",
            "type": "images"
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "links": {
            "self": "http://example.com/streams/302f6fdd-bf31-4cd7-91db-baf45fb93de0/relationships/object",
            "home": "http://example.com/home"
        }
    }

Upload a new version of a stream
--------------------------------

``POST /streams/version/{object_id}/{file_name}`` uploads a new version of a file for an existing media object.
The version counter is incremented automatically (``MAX + 1``). The first version for an object is ``1``.

.. http:post:: /streams/version/(object_id)/(file_name)

    Upload a new version of a file, passing it as binary in `body`.
    Returns ``409 Conflict`` if the incoming file (SHA-1 hash) is identical to the latest existing version.

    :query private_url: (optional) ``true`` or ``false`` — whether the uploaded stream should use a private URL.

**Example request**:

.. sourcecode:: http

    POST /streams/version/42/myfile-v2.png HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: image/png
    Authorization: Bearer <jwt>

**Example response**:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/vnd.api+json
    Location: http://example.com/streams/7a1b2c3d-4e5f-6789-abcd-ef0123456789

    {
        "data": {
            "id": "7a1b2c3d-4e5f-6789-abcd-ef0123456789",
            "type": "streams",
            "attributes": {
                "file_name": "myfile-v2.png",
                "mime_type": "image/png",
                "version": 2
            },
            "meta": {
                "..." : "..."
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "http://example.com/streams/7a1b2c3d-4e5f-6789-abcd-ef0123456789/object",
                        "self": "http://example.com/streams/7a1b2c3d-4e5f-6789-abcd-ef0123456789/relationships/object"
                    }
                }
            }
        }
    }

Replace the latest version of a stream
---------------------------------------

``PATCH /streams/version/{object_id}/{file_name}`` replaces the latest version of a stream in place.
No new version number is created — the existing latest version record is overwritten.

.. http:patch:: /streams/version/(object_id)/(file_name)

    Replace the latest version of a stream, passing the new file as binary in `body`.
    Returns ``409 Conflict`` if the file content (SHA-1 hash) is identical to the existing latest version.
    Returns ``404 Not Found`` if no streams exist for the given object.

    :query private_url: (optional) ``true`` or ``false`` — whether the stream should use a private URL.

**Example request**:

.. sourcecode:: http

    PATCH /streams/version/42/myfile-v2.png HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: image/png
    Authorization: Bearer <jwt>

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "7a1b2c3d-4e5f-6789-abcd-ef0123456789",
            "type": "streams",
            "attributes": {
                "file_name": "myfile-v2.png",
                "mime_type": "image/png",
                "version": 2
            },
            "meta": {
                "..." : "..."
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "http://example.com/streams/7a1b2c3d-4e5f-6789-abcd-ef0123456789/object",
                        "self": "http://example.com/streams/7a1b2c3d-4e5f-6789-abcd-ef0123456789/relationships/object"
                    }
                }
            }
        }
    }

Get an image
------------

.. http:get:: /images/{image_id}

    Get detailed data for an image by id.

**Example request**:

.. sourcecode:: http

    GET /images/6 HTTP/1.1
    Host: example.com

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "6",
            "type": "images",
            "attributes": {
                "..." : "..."
            },
            "meta": {
                "..." : "..."
            },
            "relationships": {
                "streams": {
                    "data": [
                        {
                            "id": "302f6fdd-bf31-4cd7-91db-baf45fb93de0",
                            "type": "streams"
                        }
                    ],
                    "links": {
                        "related": "http://example.com/images/6/streams",
                        "self": "http://example.com/images/6/relationships/streams"
                    }
                }
            }
        },
        "links": {
            "..." : "..."
        },
        "meta": {
            "..." : "..."
        },
        "included": [
            {
                "id": "302f6fdd-bf31-4cd7-91db-baf45fb93de0",
                "type": "streams",
                "attributes": {
                    "..." : "..."
                },
                "meta": {
                    "..." : "..."
                },
                "links": {
                    "..." : "..."
                },
                "relationships": {
                    "..." : "..."
                }
            }
        ]
    }
