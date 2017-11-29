Schema ``/model/schema``
========================

A :term:`JSON SCHEMA` (draft 06) representation for each object and resource type can be retrieved with ``GET /model/schema/{type}``.

This is a read-only endpoint: only `GET` method for a single object type or resource is available.

.. http:get:: /model/schema/{type}

* ``{type}`` object type or resource ``name``

**Example request: role schema**:

.. sourcecode:: http

    GET /model/schema/roles HTTP/1.1
    Host: api.example.com
    Accept: application/schema+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/schema+json

    {
        "definitions": {},
        "$id": "http://api.example.com/model/schema/roles",
        "$schema": "http://json-schema.org/draft-06/schema#",
        "type": "object",
        "properties": {
            "name": {
                "$id": "/properties/name",
                "type": "string",
                "maxLength": 32,
                "default": null
            },
            "description": {
                "$id": "/properties/description",
                "type": "string",
                "default": null
            },
            "unchangeable": {
                "$id": "/properties/unchangeable",
                "type": "boolean",
                "default": "0",
                "isMeta": true
            },
            "created": {
                "$id": "/properties/created",
                "type": "string",
                "format": "date-time",
                "default": null,
                "isMeta": true
            },
            "modified": {
                "$id": "/properties/modified",
                "type": "string",
                "format": "date-time",
                "default": null,
                "isMeta": true
            }
        },
        "required": [
            "name"
        ]
    }

Valid input values for ``{type}`` are:

* every object type ``name`` in its canonical plural form, i.e. ``documents``, ``profiles``, ``users``...
* some selected resource names: ``roles``, ``streams``, ``applications``

This representation defines the data structure of each object and resource available in terms of their properties. Relations between objects and resources are not (yet) displayed here.

For an in-depth explanation please have a look at the excellent `JSON Schema documentation <http://json-schema.org/documentation.html>`_.

Please note that this representation is agnostic of the actual JSON data format used, whether it's :term:`JSON API` or ``JSON-LD`` or ``GraphQL``.

Hence it will not match against the :term:`JSON API` response you will get, but there is only a simple rule to follow in this case.

The custom keyword ``"isMeta"`` identifies `meta` properties, when its value is **true**,  that will appear in the `"meta"` section of a typical JSON API response. All other properties will instead be found in the `"attributes"` section.
