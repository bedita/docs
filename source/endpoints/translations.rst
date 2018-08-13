Translations ``/translations``
==============================

I18n data management is provided by ``translations`` endpoint.
It provides:

 - add a translation
 - modify a translation
 - remove a translation
 - get translations with filters

Add a translation
-----------------

.. http:POST:: /translations

You create a translation with a ``POST /translations``, specifying in payload some necessary data:

 - `object_id`: the identifier of the object you want to translate
 - translation `status` (can be: "on", "off", "draft")
 - `lang`: the 2 chars code for the lang of the translation
 - `translated_field`: the field-value pairs representing the translation per field

You can see all together in the following example.

**Example request (create a translation)**:

.. sourcecode:: http

    POST /translations HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "translations",
            "attributes": {
                "object_id": "{objectId}",
                "status": "draft",
                "lang": "sp",
                "translated_fields": {
                    "title": "titulo uno",
                    "description": "descripción uno"
                }
            }
        }
    }

** Example response**:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{translationId}",
            "type": "translations",
            "attributes": {
                "object_id": {objectId},
                "lang": "sp",
                "status": "draft",
                "translated_fields": {
                    "title": "titulo uno",
                    "description": "descripción uno"
                }
            },
            "meta": {
                "created": "2018-08-13T11:01:49+00:00",
                "modified": "2018-08-13T11:01:49+00:00",
                "created_by": 1,
                "modified_by": 1
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "https://example.com/translations/1/object",
                        "self": "https://example.com/translations/1/relationships/object"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/translations",
            "home": "https://example.com/home"
        }
    }
