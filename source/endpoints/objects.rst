Objects ``/objects``
====================

``/objects`` base endpoint provides a set of methods dedicated to abstract objects management.
``objects`` is an abstract model for BEdita. Every object created through data modelling are instances of ``objects``.
You can use ``GET /objects`` when you want to retrieve data for objects whose types extend ``objects``.

This endpoint provides:

 - get a list of objects (multiple types at the same time), using filters
 - retrieve data of a single object by unique identifier (allowed, but you can also use ``GET /{objectTypeName}/{objectId}``)
 - remove an object (allowed, but discouraged: use ``DELETE /{objectTypeName}/{objectId}`` instead)

To create, modify or delete data for objects whose types extend ``objects``, you use dedicated endpoint ``/{objectTypeName}``, created through modelling (for more details, see :ref:`api-modelling`):

 - ``POST /{objectTypeName}``: create data
 - ``PATCH /{objectTypeName}/{objectId}``: update data
 - ``DELETE /{objectTypeName}/{objectId}``: move to trash
 - ``DELETE /trash/{objectId}``: remove from trash

**Note**: in the following sections, ``{objectId}`` and ``(object_id)`` are sample placeholders for the unique identifier of the object, typically an integer number.
Other placeholders used in this page are ``{objectTypeName}``, ``{status}``, ``{uname}``, ``{title}``, ``{createdDate}``, ``{modifiedDate}``, ``{createdBy}``, ``{modifiedBy}``, ``{status}``, ``{revisionNumber}`` etc.
In the examples, response data contains a reduced number of fields, for better readability.

Get a collection of objects
---------------------------

.. http:get:: /objects

The ``/objects`` endpoint can be used to retrieve a collection of objects of different types.
Available filters are :ref:`filter-type`, :ref:`filter-field` and :ref:`filter-search`.

**Example request (get all objects)**:

.. sourcecode:: http

    GET /objects HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "{objectId1}",
                "type": "{objectTypeName1}",
                "attributes": {
                    "status": "{status1}",
                    "uname": "{uname1}",
                    "title": "{title1}"
                },
                "meta": {
                    "created": "{createdDate1}",
                    "modified": "{modifiedDate1}",
                    "created_by": "{createdBy1}",
                    "modified_by": "{modifiedBy1}"
                },
                "links": {
                    "self": "https://example.com/{objectTypeName1}/{objectId1}"
                },
                "relationships": {
                    "{relationName1}": {
                        "links": {
                            "related": "https://example.com/{objectTypeName1}/{objectId1}/{relationName1}",
                            "self": "https://example.com/{objectTypeName1}/{objectId1}/relationships/{relationName1}"
                        }
                    },
                    "{relationName2}": {
                        "links": {
                            "related": "https://example.com/{objectTypeName1}/{objectId1}/{relationName2}",
                            "self": "https://example.com/{objectTypeName1}/{objectId1}/relationships/{relationName2}"
                        }
                    },
                }
            },
            {
                "id": "{objectId2}",
                "type": "{objectTypeName2}",
                "attributes": {
                    "status": "{status2}",
                    "uname": "{uname2}",
                    "title": "{title2}"
                },
                "meta": {
                    "created": "{createdDate2}",
                    "modified": "{modifiedDate2}",
                    "created_by": "{createdBy2}",
                    "modified_by": "{modifiedBy2}"
                },
                "links": {
                    "self": "https://example.com/{objectTypeName2}/{objectId2}"
                },
                "relationships": {
                    "{relationName1}": {
                        "links": {
                            "related": "https://example.com/{objectTypeName2}/{objectId2}/{relationName1}",
                            "self": "https://example.com/{objectTypeName2}/{objectId2}/relationships/{relationName1}"
                        }
                    },
                    "{relationName2}": {
                        "links": {
                            "related": "https://example.com/{objectTypeName2}/{objectId2}/{relationName2}",
                            "self": "https://example.com/{objectTypeName2}/{objectId2}/relationships/{relationName2}"
                        }
                    },
                }
            },
        ],
        "links": {
            "self": "https://example.com/objects",
            "home": "https://example.com/home",
            "first": "https://example.com/objects",
            "last": "https://example.com/objects?page=1",
            "prev": null,
            "next": "https://example.com/objects?page=1"
        },
        "meta": {
            "pagination": {
                "count": 2,
                "page": 1,
                "page_count": 2,
                "page_items": 20,
                "page_size": 20
            },
            "schema": {
                "{objectTypeName1}": {
                    "$id": "https://example.com/model/schema/{objectTypeName1}",
                    "revision": "{revisionNumber1}"
                },
                "{objectTypeName2}": {
                    "$id": "https://example.com/model/schema/{objectTypeName2}",
                    "revision": "{revisionNumber2}"
                }
            }
        }
    }

In the example above, api returns 2 objects (``{objectId1}`` and ``{objectId2}``), of different types (``{objectTypeName1}`` and ``{objectTypeName2}``).
``{relationName1}`` and ``{relationName2}}`` are the relationships defined in the example.
For more details, see :ref:`api-responses`

Get single object
-----------------

.. http:get:: /objects/(object_id)

Get details for a single object by its unique identifier.

**Example request (get a single object by id)**:

.. sourcecode:: http

    GET /objects/{objectId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{objectId}",
            "type": "{objectTypeName}",
            "attributes": {
                "status": "{status}",
                "uname": "this-is-a-test-object",
                "title": "this is a test object"
            },
            "meta": {
                "created": "{createdDate}",
                "modified": "{modifiedDate}",
                "created_by": "{createdBy}",
                "modified_by": "{modifiedBy}"
            },
            "relationships": {
                "parents": {
                    "links": {
                        "related": "https://example.com/{objectTypeName}/{objectId}/parents",
                        "self": "https://example.com/{objectTypeName}/{objectId}/relationships/parents"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/objects/{objectId}",
            "home": "https://example.com/home"
        },
        "meta": {
            "schema": {
                "documents": {
                    "$id": "https://example.com/model/schema/{objectTypeName}",
                    "revision": "{revisionNumber}"
                }
            }
        }
    }

Remove objects
--------------

.. http:DELETE:: /objects/(object_id)

You can move object to trashcan (*soft delete*) using ``DELETE /objects/{objectId}``, with empty body.
You can either do the same task using ``DELETE /(object_type_name)/{objectId}``, with empty body.

When delete succeeds, ``204 No Content`` response is returned.

**Example request (delete an object)**:

.. sourcecode:: http

    DELETE /objects/{objectId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 204 No Content
