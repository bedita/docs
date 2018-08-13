Translations ``/translations``
==============================

I18n data management is provided by ``translations`` endpoint.
It provides:

 - get translations with filters
 - add a translation
 - modify a translation
 - remove a translation

**Note**: in the following sections, ``(translation_id)`` and ``{translationId}`` are sample placeholders for the unique identifier of the translation, typically an integer number;
``{objectId}`` is a sample placeholder for the unique identifier of the object translated.

Get translation data
--------------------

.. http:GET:: /translations
.. http:GET:: /translations/(translation_id)

Retrieve all translations using ``GET /translations``, or a single one using ``GET /translations/(translation_id)``.

**Example request (get all translations)**:

.. sourcecode:: http

    GET /translations HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "{translationId}",
                "type": "translations",
                "attributes": {
                    "object_id": "{objectId}",
                    "lang": "sp",
                    "status": "draft",
                    "translated_fields": {
                        "title": "nuevo título",
                        "description": "nueva descripción"
                    }
                },
                "meta": {
                    "created": "2018-08-13T14:13:46+00:00",
                    "modified": "2018-08-13T14:13:52+00:00",
                    "created_by": 1,
                    "modified_by": 1
                },
                "links": {
                    "self": "https://example.com/translations/{translationId}"
                },
                "relationships": {
                    "object": {
                        "links": {
                            "related": "https://example.com/translations/{translationId}/object",
                            "self": "https://example.com/translations/{translationId}/relationships/object"
                        }
                    }
                }
            }
        ],
        "links": {
            "self": "https://example.com/translations",
            "home": "https://example.com/home",
            "first": "https://example.com/translations",
            "last": "https://example.com/translations",
            "prev": null,
            "next": null
        },
        "meta": {
            "pagination": {
                "count": 1,
                "page": 1,
                "page_count": 1,
                "page_items": 1,
                "page_size": 20
            }
        }
    }

Add a translation
-----------------

.. http:POST:: /translations

You create a translation with a ``POST /translations``, specifying in payload body some necessary data:

 - ``type``: "translations". The endpoint.
 - ``object_id``: the identifier of the object you want to translate
 - ``status`` of translation; it can be:"on", "off" or "draft"
 - ``lang``: the 2 chars code for the lang of the translation
 - ``translated_fields``: the field-value pairs representing the translation per field

When creation succeeds, ``201 Created`` response is returned.
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
                    "title": "título uno",
                    "description": "descripción uno"
                }
            }
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 201 Created
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{translationId}",
            "type": "translations",
            "attributes": {
                "object_id": "{objectId}",
                "lang": "sp",
                "status": "draft",
                "translated_fields": {
                    "title": "título uno",
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
                        "related": "https://example.com/translations/{translationId}/object",
                        "self": "https://example.com/translations/{translationId}/relationships/object"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/translations",
            "home": "https://example.com/home"
        }
    }

Modify a translation
--------------------

.. http:PATCH:: /translations/(translation_id)

Translation can be modified using ``PATCH /translations/(translation_id)``, specifying in payload body some necessary data:

 - ``id``: the translation unique identifier
 - ``type``: "translations". The endpoint.
 - ``translated_fields``: the field-value pairs representing the translation per field

When patch succeeds, ``200 OK`` response is returned.

**Example request (modify a translation)**:

.. sourcecode:: http

    PATCH /translations/{translationId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{translationId}",
            "type": "translations",
            "attributes": {
                "translated_fields": {
                    "title": "nuevo título",
                    "description": "nueva descripción"
                }
            }
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{translationId}",
            "type": "translations",
            "attributes": {
                "object_id": "{objectId}",
                "lang": "sp",
                "status": "draft",
                "translated_fields": {
                    "title": "nuevo título",
                    "description": "nueva descripción"
                }
            },
            "meta": {
                "created": "2018-08-13T14:13:46+00:00",
                "modified": "2018-08-13T14:13:52+00:00",
                "created_by": 1,
                "modified_by": 1
            },
            "relationships": {
                "object": {
                    "links": {
                        "related": "https://example.com/translations/{translationId}/object",
                        "self": "https://example.com/translations/{translationId}/relationships/object"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/translations/{translationId}",
            "home": "https://example.com/home"
        }
    }

Remove a translation
--------------------

.. http:DELETE:: /translations/(translation_id)

Move translation to trash (*soft delete*) using ``DELETE /translations/(translation_id)``, with empty body.

When delete succeeds, ``204 No Content`` response is returned.

**Example request (delete a translation)**:

.. sourcecode:: http

    DELETE /translations/{translationId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 204 No Content
