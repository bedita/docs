Properties ``/model/properties``
================================

With this endpoint you will be able to define object properties: create new ones, disable or hide others, update and remove.

.. note::

    There are two kind of properties

    * ``static`` properties that match actual database columns and that cannot be changed or removed, but can be hidden
    * ``dynamic`` properties defined at project level

    In other words: ``static`` are predefined properties set at the database level common to any project, ``dynamic`` are custom properties available on the current project only.

.. _api-model-properties:

Create a property
-----------------

To add a new _dynamic_ object property you can use ``POST /model/properties`` endpoint.

.. http:post:: /model/properties

**Example request: create new property**:

.. sourcecode:: http

    POST /model/properties HTTP/1.1
    Host: api.example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "properties",
            "attributes": {
                "name": "nickname",
                "description": "Profile nickname",
                "property_type_name": "string",
                "object_type_name": "profiles"
            }
        }
    }

Required attributes in properties creation are:

* ``name`` property name,  must be in lowered `snake_case` and must not create collisions with other property names already registered on ``object_type_name``, and in its parent types and subtypes
* ``property_type_name`` selected property type, must be a valid name avilable among :doc:`property_types`
* ``object_type_name`` object type that will have this property

Expected response is ``HTTP/1.1 201 OK``, with ``application/vnd.api+json`` body data representing the property just created.

If property ``name`` already exists or data is not valid (i.e. lacks of required fields or inconsistent input), POST fails and response will be ``400 Bad Request - Invalid data``.

Get a property
--------------

You can obtain a single property by using ``GET /model/properties/{id}`` endpoint.

.. http:get:: /model/properties/{id}

* ``{id}`` property id, numeric or hash

**Example request get property**:

.. sourcecode:: http

    GET /model/properties/1 HTTP/1.1
    Host: api.example.com
    Accept:id/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "1",
            "type": "properties",
            "attributes": {
                "name": "nickname",
                "description": "Profile nickname",
                "property_type_name": "string",
                "object_type_name": "profiles"
            },
            "meta": {
                "created": "2017-11-23T14:24:11+00:00",
                "modified": "2017-11-23T14:24:11+00:00"
            }
        },
        "links": {
            "self": "http://api.example.com/model/properties/1",
            "home": "http://api.example.com/home"
        }
    }

Expected HTTP status response is ``200 OK``, if property is not found response will be ``404 Not Found``

View properties list
--------------------

You can retrieve list of properties using ``GET /model/properties``. Common filters like :ref:`filter-field` or :ref:`filter-search` may come in handy.

A special ``filter[object_type]`` can be used to get properties of a particular object type only.

.. http:get:: /model/object_types

**Example request get enabled object types**:

.. sourcecode:: http

    GET /model/object_types?filter[object_type]=documents HTTP/1.1
    Accept: application/vnd.api+json

Response will contain an array of ``properties`` in typical list format as shown in :ref:`api-responses` belonging to ``documents`` type only

In this response you will note two important things:

* some items contain an hash format id like ``"id": "608e814b-e4d7-57c2-8599-9692803e30bc"`` and some a classic numeric one; has format _ids_   represent ``static`` fields, i.e. fields that match actual database columns that in general cannot be removed, but can be hidden - see ``hidden`` object types attribute in :ref:`api-model-object-types`; instead pure numeric _ids_ represent pure ``dynamic`` properties defined at project level; ``static`` will be present in any project, ``dynamic`` are custom ones available on the current project only.
* ``"object_type_name"`` will refer to the actual object type owning the property, it can be an abstract type; as a consequence ``"objects"`` and ``"media"`` and other custom ``abstract`` types may appear as ``"object_type_name"`` also making a request like ``filter[object_type]=documents``: properties displayed will have the requested type and its abstract parent types as ``object_type_name``.

An additional filter may be used to select `static` or `dynamic` properties, namely ``filter[type]=static`` or ``filter[type]=dynamic`` to view only a properties subset.

Modify a property
-----------------

To change a property there is a ``PATCH /model/properties/{{id}}`` method available.

Remember that only ``dynamic`` properties may be modified, whereas ``static`` ones are immutable, but can be hidden.

.. http:patch:: /model/properties/{{id}}

* ``{id}`` dynamic property numeric ``id``

**Example request: modify a property**:

In this example we will simply disable the newly created property and change its description

.. sourcecode:: http

    PATCH /model/properties/1 HTTP/1.1
    Content-Type: application/json

    Accept: application/vnd.api+json

    {
        "data": {
            "id": "1",
            "type": "properties",
            "attributes": {
                "name": "nickname",
                "description": "A brand new property description"
            }
        }
    }

Response status ``200 OK`` expected upon success and complete modified property is returned like in `Get a property`_.

Please note that once a property has been used in your project changing some attributes may not be changed in order to avoid conflicts and errors, namely: ``name``, ``property_type_name`` and ``object_type_name``.

Remove a property
-----------------

You can permanently remove a property invoking ``DELETE /model/properties/{id}``. Only ``dynamic`` properties may be removed.

Please note that this operation cannot be reversed!

.. http:delete:: /model/properties/(id)

* ``{id}`` dynamic property numeric ``id``

**Example request: delete property**:

.. sourcecode:: http

    DELETE /model/properties/1 HTTP/1.1

Expected HTTP status response is ``204 No Content``.

If property is not found, response will be ``404 Not Found``, if delete operation is not allowed a ``403 Forbidden`` will be sent.

.. sourcecode:: http

    HTTP/1.1 204 No Content
