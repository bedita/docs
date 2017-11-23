Relations ``/model/relations``
==============================

``/model/relations`` endpoint manages relations between objects types.

This is one of the most important features in BEdita: you can create dynamic semantic relations between object types, adding constraint rules and parameters without any data model or database modification.

Relations in BEdita have been heavily inspired by semantic web `RDF triples <https://www.w3.org/TR/2014/NOTE-rdf11-primer-20140624/#section-triple>`_.

Every relation should be espressed in a form ``{subject} {predicate} {object}`` where both ``{subject}`` and ``{object}`` are BEdita objects and ``{predicate}`` is the relation name.
In BEdita for every relation an inverse one is also created, this will be more clear in the example below.

With this endpoint you can:

* create, update/remove an object relation
* list available relations
* handle which object types are involved in a relation

Create a relation
-----------------

Creation of a new object type happens through ``POST /model/relations`` endpoint.

.. http:post:: /model/relations

**Example request - create relation**:

.. sourcecode:: http

    POST /model/relations HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "relations",
            "attributes": {
                "name": "owner_of",
                "label": "Owner of",
                "inverse_name": "belong_to",
                "inverse_label": "Belong to",
                "description": "Cat owner relation"
            }
        }
    }

Required attributes creating a new relation are:

* ``name`` principal relation name
* ``inverse_name`` inverse relation name

Both attributes must be different and unique inside the project, should differ from property names of object types involved in this relation and as naming convention should also be expressed as a _predicate_ linking object types together.

They also must be in `lowered snake_case <https://en.wikipedia.org/wiki/Snake_case>`_ format as internal naming convention.

Our goal in this example, altough not yet complete, is to create the relation ``[users] [owner_of] [cats]`` and its inverse ``[cats] [belong_to] [users]``.

Other optional attributes are

* ``label`` alternative text to show instead of ``name``
* ``inverse_label`` alternative text to show instead of ``inverse_name``
* ``description`` optional description of the relation

Expected response is ``HTTP/1.1 201 OK``, with ``application/vnd.api+json`` body data representing relation just created.

If the relation already exists or data is not valid (i.e. lacks of required fields or inconsistent input), POST fails and response will be ``400 Bad Request - Invalid data``.

.. _api-model-relations-get:

Get a single relation
---------------------

You can obtain a single relations by invoking ``GET /model/relations/{relation}``.

.. http:get:: /model/relations/{relation}

* ``{relation}`` can be a numeric id of the relation, its ``name`` or ``inverse_name``

**Example request (get a relation)**:

.. sourcecode:: http

    GET /model/relations/owner_of HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "1",
            "type": "relations",
            "attributes": {
                "name": "owner_of",
                "label": "Owner of",
                "inverse_name": "belong_to",
                "inverse_label": "Belong to",
                "description": "Cat owner relation",
                "params": {}
            },
            "relationships": {
                "left_object_types": {
                    "links": {
                        "related": "http://api.example.com/model/relations/1/left_object_types",
                        "self": "http://api.example.com/model/relations/1/relationships/left_object_types"
                    }
                },
                "right_object_types": {
                    "links": {
                        "related": "http://api.example.com/model/relations/1/right_object_types",
                        "self": "http://api.example.com/model/relations/1/relationships/right_object_types"
                    }
                }
            }
        },
        "links": {
            "self": "http://api.example.com/model/relations/owner_of",
            "home": "http://api.example.com/home"
        }
    }

Example reponse shows us something interesting in ``"relationships"`` section:

* ``"left_object_types"`` links object types to use on _the_ left side of the relation, think of the ``subject`` in the ``subject predicate object`` expression, i.e. object types that are ``owner_of`` other object types (on the _right_ side)
* ``"right_object_types"`` links object types to use on the _right_ side of the relation, think of the ``object`` in the ``subject predicate object`` expression, i.e. object types that ``belong_to`` object types on the _left_ side

Relations list
--------------

To retrieve a list of relations you can simply invoke ``GET /model/relations`` and use common filters like :ref:`filter-field` or :ref:`filter-search`

.. http:get:: /model/relations

**Example request: get relations**:

.. sourcecode:: http

    GET /model/relations?filter[name]=owner_of HTTP/1.1
    Accept: application/vnd.api+json

Response will contain an array of ``relations`` in typical list format as shown in :ref:`api-responses`.
In this particular case response content in ``"data"`` section will be the same as in the previous example, here below a reduced version of it for brevity.

**Example response: get relations**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [{
            "id": "1",
            "type": "relations",
            "attributes": {
                "name": "owner_of",
                "..." : "..."
            },
            "relationships": {
                "..." : "..."
            }
        }],
        "links": {
            "..." : "..."
        },
        "meta": {
            "..." : "..."
        }
    }

Handle left and right object types
----------------------------------

Relation modeling in BEdita is expressed, as seen before, in ``{subject} {predicate} {object}`` form wich is translated to ``{left object types} {relation name} {right object types}`` on a left to right expression flow.

To achieve this we need to manipulate `left` and `right` side of the expression: both may contain a list of object types involved in the relation.

Add object types
^^^^^^^^^^^^^^^^

You may add object types to left side ``POST /model/relations/{{relation}}/relationships/left_object_types`` and right side with
``POST /model/relations/{{relation}}/relationships/right_object_types``

.. http:post:: /model/relations/{{relation}}/relationships/left_object_types

* ``{relation}`` can be a numeric id of the relation, its ``name`` or ``inverse_name``

**Example request: add left object types**:

.. sourcecode:: http

    POST /model/relations/owner_of/relationships/left_object_types HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "type": "object_types",
                "id": "3"
            },
            {
                "type": "object_types",
                "id": "2"
            }
        ]
    }

In this example ``users`` (with _id_ 3) and ``profiles`` (_id_ 2) have been added as left side object types to the relation created before.

Expected response is ``200 OK`` upon success, while response will be contain only a ``"links"`` section pointing to the list of left/right object types for the current relation.

Get object types
^^^^^^^^^^^^^^^^

To retrieve object types involved in a relation you can invoke ``GET /model/relations/{{relation}}/left_object_types`` for the left side and
``GET /model/relations/{{relation}}/right_object_types`` for the right side.

``{relation}`` as usual can be a numeric id of the relation, its ``name`` or ``inverse_name`` and response will present a list of object types in ``"data"`` section.

Replace object types
^^^^^^^^^^^^^^^^^^^^

By invoking ``PATCH /model/relations/{{relation}}/relationships/left_object_types`` you will replace **all** object types on the left side with a provided list. Same thing will happen with ``PATCH /model/relations/{{relation}}/relationships/right_object_types``.

.. http:patch:: /model/relations/{{relation}}/relationships/right_object_types

* ``{relation}`` can be a numeric id of the relation, its ``name`` or ``inverse_name``

**Example request: replace right object types**:

.. sourcecode:: http

    PATCH /model/relations/owner_of/relationships/left_object_types HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "type": "object_types",
                "id": "13"
            }
        ]
    }

With this method we have completed our relation putting our custom object type seen in :ref:`api-model-object-types` on the righ side.

Now we have: ``{users | profiles} {owner_of} {cats}`` and inverse from right to left ``{cats} {belong_to} {users | profiles}``.

Expected response is ``200 OK`` upon success, while response will be contain only a ``"links"`` section pointing to the list of left/right object types for the current relation.

Remove object types
^^^^^^^^^^^^^^^^^^^

To remove an object type from a relation you can call ``DELETE /model/relations/{{relation}}/relationships/left_object_types`` (left side) or
``DELETE /model/relations/{{relation}}/relationships/right_object_types`` (right side) specifying an object type id in the request body.

.. http:delete:: /model/relations/{{relation}}/relationships/left_object_types

* ``{relation}`` can be a numeric id of the relation, its ``name`` or ``inverse_name``

**Example request: remove left object types**:

.. sourcecode:: http

    DELETE /model/relations/owner_of/relationships/left_object_types HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "object_types",
            "id": "2"
        }
    }

Here we have removed ``profiles`` (_id_ 2) from the left side. Now our relation will look like ``{users} {owner_of} {cats}``.

Expected HTTP status response is ``204 No Content`` and an empty body is returned.

If object type is not a valid left/right type response will be ``400 Bad Request``.

Operation is not allowed if an object of the type you want to remove is already used in the relation. A ``403 Forbidden`` error will be sent in this case.

Modify a relation
---------------------

You can modify a relation by using ``PATCH /model/relations/{relation}`` endpoint.

.. http:patch:: /model/relations/{relation}

* ``{relation}`` can be a numeric id of the relation, its ``name`` or ``inverse_name``

**Example request: modify a relation**:

In this example we will simply disable the newly created type and chage its description

.. sourcecode:: http

    PATCH /model/relations/owner_of HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 1,
            "type": "relations",
            "attributes": {
                "description" : "Link users owning cats"
            }
        }
    }

Response status ``200 OK`` expected upon success and complete modified relation is returned like in :ref:`api-model-relations-get`.

Remove a relation
-----------------

You can permanently delete a relation by invoking ``DELETE /model/relations/{relation}``.

This operation cannot be reversed and will not be allowed if actual object relations of this kind exist.

.. http:delete:: /model/relations/(relation)

* ``{relation}`` can be a numeric id of the relation, its ``name`` or ``inverse_name``

**Example request: delete relation**:

.. sourcecode:: http

    DELETE /model/relations/owner_of HTTP/1.1
    Host: api.example.com

Expected HTTP status response is ``204 No Content``.

If relation is not not found, response will be ``404 Not Found``, if delete operation is not allowed a ``403 Forbidden`` will be sent.

.. sourcecode:: http

    HTTP/1.1 204 No Content
