Annotations ``/annotations``
============================

``/annotations`` endpoint provides a way to handle annotations management.
``annotations`` are structured info that can be related to BEdita objects.

This endpoint provides:

 - get a list of annotations
 - retrieve data of a single annotation by unique identifier
 - create an annotation
 - modify an annotation
 - remove an annotation

**Note**: in the following sections, ``{annotationId}`` and ``(annotation_id)`` are sample placeholders for the unique identifier of the annotation, typically an integer number.
``{objectId}`` is a sample placeholders for the unique identifier of the object.
Other placeholders used in this page are ``{objectTypeName}``, ``{description}``, ``{userId}``, ``{createdDate}``, ``{modifiedDate}``, etc.
In the examples, response data contains a reduced number of fields, for better readability.

Get a collection of annotations
-------------------------------

.. http:get:: /annotations

Use ``GET /annotations`` to retrieve a collection of annotations.
Available filters are :ref:`filter-field` and :ref:`filter-search`.

**Example request (get all annotations)**:

.. sourcecode:: http

    GET /annotations HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "{annotationId}",
                "type": "annotations",
                "attributes": {
                    "object_id": "{objectId}",
                    "description": "{description}",
                    "params": null
                },
                "meta": {
                    "user_id": "{userId}",
                    "created": "{createdDate}",
                    "modified": "{modifiedDate}"
                },
                "links": {
                    "self": "https://example.com/annotations/{annotationId}"
                },
                "relationships": {
                    "object": {
                        "links": {
                            "related": "https://example.com/annotations/{annotationId}/object",
                            "self": "https://example.com/annotations/{annotationId}/relationships/object"
                        }
                    },
                }
            }
        ],
        "links": {
            "self": "https://example.com/annotations",
            "home": "https://example.com/home",
            "first": "https://example.com/annotations",
            "last": "https://example.com/annotations",
            "prev": null,
            "next": null
        },
        "meta": {
            "pagination": {
                "count": 2,
                "page": 1,
                "page_count": 1,
                "page_items": 1,
                "page_size": 20
            }
        }
    }

Get single annotation
---------------------

.. http:get:: /annotations/(annotation_id)

Get details for a single annotation by its unique identifier.

**Example request (get a single annotation by id)**:

.. sourcecode:: http

    GET /annotations/{annotationId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{annotationId}",
            "type": "annotations",
            "attributes": {
                "object_id": "{objectId}",
                "description": "{description}",
                "params": null
            },
            "meta": {
                "user_id": "{userId}",
                "created": "{createdDate}",
                "modified": "{modifiedDate}"
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "https://example.com/annotations/{annotationId}/object",
                        "self": "https://example.com/annotations/{annotationId}/relationships/object"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/annotations/{annotationId}",
            "home": "https://example.com/home"
        }
    }

Create an annotation
--------------------

.. http:POST:: /annotations

Annotation must be referenced to an object and it must have at least a ``description``.
To create a new annotation, you use ``POST /annotations``, specifying in the payload:

 - the annotations type (``data.type``)
 - the referenced object (``data.attributes.object_id``)
 - the annotation description (``data.attributes.description``)

On succeed, response http status is ``201 Created``.

**Example request (create a new annotation)**:

.. sourcecode:: http

    POST /annotations HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": {
            "type": "annotations",
            "attributes": {
                "object_id": "{objectId}",
                "description": "a note"
            }
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{annotationId}",
            "type": "annotations",
            "attributes": {
                "object_id": "{objectId}",
                "description": "a note",
                "params": null
            },
            "meta": {
                "user_id": "{userId}",
                "created": "{createdDate}",
                "modified": "{modifiedDate}"
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "https://example.com/annotations/{annotationId}/object",
                        "self": "https://example.com/annotations/{annotationId}/relationships/object"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/annotations",
            "home": "https://example.com/home"
        }
    }


Modify an annotation
--------------------

.. http:PATCH:: /annotations/(annotation_id)

Annotation can be modified using ``PATCH /annotations/{annotationId}``.
Expected fields in body payload:

 - the annotation identifier (``data.id``)
 - the annotations type (``data.type``)
 - the annotation description (``data.attributes.description``)

On succeed, response http status is ``200 OK``.

A basic example follows.

**Example request (modify an annotation)**:

.. sourcecode:: http

    PATCH /annotations/{annotationId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": {
            "id": "{annotationId}",
            "type": "annotations",
            "attributes": {
                "description": "another note"
            }
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{annotationId}",
            "type": "annotations",
            "attributes": {
                "object_id": "{objectId}",
                "description": "another note",
                "params": null
            },
            "meta": {
                "user_id": "{userId}",
                "created": "{createdDate}",
                "modified": "{modifiedDate}"
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "https://example.com/annotations/{annotationId}/object",
                        "self": "https://example.com/annotations/{annotationId}/relationships/object"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/annotations/{annotationId}",
            "home": "https://example.com/home"
        }
    }

Remove annotations
------------------

.. http:DELETE:: /annotations/(annotation_id)

You can move annotations to trashcan (*soft delete*) using ``DELETE /annotations/{annotationId}``, with empty body.

When delete succeeds, ``204 No Content`` response is returned.

**Example request (delete an annotation)**:

.. sourcecode:: http

    DELETE /annotations/{annotationId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 204 No Content
