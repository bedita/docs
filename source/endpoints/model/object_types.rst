Object Types ``/model/object_types``
====================================

Through this endpoint you will be able to create new object types, modify, enable/disable or remove an object type.

.. _api-model-object-types:

Create an object type
---------------------

Creation of a new object type happens through ``POST /model/object_types`` endpoint.

.. http:post:: /model/object_types

**Example request (create `cats` type)**:

.. sourcecode:: http

    POST /model/object_types HTTP/2
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "object_types",
            "attributes": {
                "name": "cats",
                "singular": "cat",
                "description": "This is a cat"
        }
    }

In its simplest form the only required attributes are:

* ``name`` object type in plural form, this is the canonical form for this type that will activate ``/cats`` endpoint
* ``singular`` singular form for type

Both must be in `lowered snake_case <https://en.wikipedia.org/wiki/Snake_case>`_ format as internal naming convention, see :ref:`api-model-naming`, and must contain at least a letter (a-z) to avoid collisions with numeric ids.
They also must be unique in your project.

* ``description`` is an optional description of your type

Expected response is ``HTTP/2 201 OK``, with ``application/vnd.api+json`` body data representing object type just created.

If the object type already exists or data is not valid (i.e. lacks of required fields or inconsistent input), POST fails and response will be ``400 Bad Request - Invalid data``.

Get single object type
----------------------

You can obtain a single type by using ``GET /model/object_types/{type}`` endpoint.

.. http:get:: /model/object_types/{type}

* ``{type}`` can be a numeric id of the object type or its ``name``

**Example request (get object types)**:

.. sourcecode:: http

    GET /model/object_types/cats HTTP/2
    Host: api.example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "13",
            "type": "object_types",
            "attributes": {
                "name": "cats",
                "singular": "cat",
                "is_abstract": false,
                "description": "This is a cat",
                "associations": null,
                "hidden": null,
                "enabled": true,
                "table": "BEdita/Core.Objects",
                "parent_name": "objects"
            },
            "meta": {
                "created": "2017-11-21T13:56:54+00:00",
                "modified": "2017-11-21T13:56:54+00:00",
                "core_type": false,
                "alias": "Cats",
                "relations": []
            },
            "relationships": {
                "left_relations": {
                    "data": [],
                    "links": {
                        "related": "http://api.example.com/model/object_types/13/left_relations",
                        "self": "http://api.example.com/model/object_types/13/relationships/left_relations"
                    }
                },
                "right_relations": {
                    "data": [],
                    "links": {
                        "related": "http://api.example.com/model/object_types/13/right_relations",
                        "self": "http://api.example.com/model/object_types/13/relationships/right_relations"
                    }
                },
                "parent": {
                    "links": {
                        "related": "http://api.example.com/model/object_types/13/parent",
                        "self": "http://api.example.com/model/object_types/13/relationships/parent"
                    }
                }
            }
        },
        "links": {
            "self": "http://api.example.com/model/object_types/cats",
            "home": "http://api.example.com/home"
        }
    }

Example response shows us some additional interesting fields:

* ``"is_abstract"`` tells us if an object type is abstract or concrete; an abstract type can only have concrete subtypes but there can be no object of this type
* ``"associations"``: list of internal entities associated with this type like ``DateRanges`` or ``Streams``, formatted as JSON array, defaults to null
* ``"hidden"``: list of core properties to hide, those properties will be ignored on every request and response for this type, defaults to null
* ``"enabled"``: is a simple flag to activate/deactivate an object type in your project; you will not be able to set it to ``false`` if objects of this type or sub-types exist
* ``"table"``: table class responsible to store your object data, expressed in `CakePHP plugin syntax <https://book.cakephp.org/3.0/en/appendices/glossary.html#term-plugin-syntax>`_, defaults to ``"BEdita/Core.Objects"``
* ``"parent_name"``: `"name"` of the parent abstract type, defaults to ``"objects"``
* ``core_type`` is a system flag indicating whether this is a ``core`` object type or a user created type on this project

Relationships available are instead:

* ``"left_relations"`` and ``"right_relations"`` representing object types with _left_ or _right_ side relations to this type, see :doc:`relations` for an axplanation
* ``"parent"`` abstract parent type of this one

Object types list
-----------------

To retrieve a list of object types you can simply use ``GET /model/object_types`` and take advantage of common filters like :ref:`filter-field` or :ref:`filter-search`

.. http:get:: /model/object_types

**Example request get enabled object types**:

.. sourcecode:: http

    GET /model/object_types?filter[enabled]=true HTTP/2
    Accept: application/vnd.api+json

Response will contain an array of ``object_types`` in typical list format as shown in :ref:`api-responses`.

Modify an object type
---------------------

You can modify an object type by using ``PATCH /model/object_types/{type}`` endpoint.

.. http:patch:: /model/object_types/{type}

* ``{type}`` can be a numeric id of the object type or its ``name``

**Example request: modify an object type**:

In this example we will simply disable the newly created type and change its description

.. sourcecode:: http

    PATCH /model/object_types/13 HTTP/2
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 13,
            "type": "object_types",
            "attributes": {
                "enabled" : false,
                "description" : "This was a cat"
            }
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 13,
            "type": "object_types",
            "attributes": {
                "username": "johndoe",
                "name": "Johnny",
                "surname": "Doe"
            },
            "meta": {
                ...
            },
            "relationships": {
                ...
            }
        },
        "links": {
            "self": "http://api.example.com/model/object_types/13",
            "home": "http://api.example.com/home"
        }
    }

``"meta"`` and ``"relationships"`` here omitted for brevity.

Remove an object type
---------------------

You can delete permanently an object type by using ``DELETE /model/object_types/{type}`` endpoint.

This operation cannot be reversed and will not be allowed if:

* one or more objects of this type exist
* ``core_type`` attribute flag is ``true``
* a subtype of this type exits

.. http:delete:: /model/object_types/(type)

* ``{type}`` can be a numeric id of the object type or its ``name``

**Example request: delete cats**:

.. sourcecode:: http

    DELETE /model/object_types/cats HTTP/2
    Host: api.example.com

Expected HTTP status response is ``204 No Content``.

If object type is not found, response will be ``404 Not Found``, if delete operation is not allowed a ``403 Forbidden`` will be sent.

.. sourcecode:: http

    HTTP/2 204 No Content
