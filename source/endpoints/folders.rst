Folders ``/folders``
====================

Folders endpoint provides folders data, structure and contents management: search, create, modify folders,
add or remove contents, reorder data, organize folders tree, etc.

Mainly it provides:

    * manage data: retrieve, create, modify or remove folders
    * manage tree: retrieve parent folder data, set or remove a parent folder association
    * manage contents: add, remove, search/get, reorder folders contents

Folders data
------------

Get data
........

.. http:get:: /folders

All folders retrieval is performed through `GET /folders`.
Folders endpoint provides several filters to obtain data.
Usual filters, like :ref:`filter-field` or :ref:`filter-search`, are available; extra filters are `roots` and `parent`.

Filter `roots` provides a method to fetch only root folders.
Usage: `GET /folders?filter[roots]`.

Filter `parent` allows you to obtain folders whose parent is a specific folder, by ID.
Usage: `GET /folders?filter[parent]={rootFolderId}`.

When the api call is successfull, response with `200 OK` http status code is returned.

**Example request**:

.. sourcecode:: http

    GET /folders?filter[roots] HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "{folderId1}",
                "type": "folders",
                "attributes": {
                    "title": "Sample root",
                },
                "meta": {
                    "created": "2018-04-09T15:03:27+00:00",
                    "modified": "2018-04-09T15:03:27+00:00",
                    "path": "/{folderId}"
                },
                "links": {
                    "self": "https://example.com/folders/{folderId1}"
                },
                "relationships": {
                    "children": {
                        "links": {
                            "related": "https://example.com/folders/{folderId1}/children",
                            "self": "https://example.com/folders/{folderId1}/relationships/children"
                        }
                    },
                    "parent": {
                        "links": {
                            "related": "https://example.com/folders/{folderId1}/parent",
                            "self": "https://example.com/folders/{folderId1}/relationships/parent"
                        }
                    }
                }
            },
            {
                "id": "{folderId2}",
                "type": "folders",
                "attributes": {
                    "title": "Root Folder",
                },
                "meta": {
                    "created": "2018-08-10T10:19:00+00:00",
                    "modified": "2018-08-10T10:19:00+00:00",
                    "path": "/{folderId2}"
                },
                "links": {
                    "self": "https://example.com/folders/{folderId2}"
                },
                "relationships": {
                    "children": {
                        "links": {
                            "related": "https://example.com/folders/{folderId2}/children",
                            "self": "https://example.com/folders/{folderId2}/relationships/children"
                        }
                    },
                    "parent": {
                        "links": {
                            "related": "https://example.com/folders/{folderId2}/parent",
                            "self": "https://example.com/folders/{folderId2}/relationships/parent"
                        }
                    }
                }
            }
        ],
        "links": {
            "self": "https://example.com/folders?filter%5Broots%5D=",
            "home": "https://example.com/home",
            "first": "https://example.com/folders?filter%5Broots%5D=",
            "last": "https://example.com/folders?filter%5Broots%5D=",
            "prev": null,
            "next": null
        },
        "meta": {
            "pagination": {
                "count": 2,
                "page": 1,
                "page_count": 1,
                "page_items": 2,
                "page_size": 20
            },
            "schema": {
                "folders": {
                    "$id": "https://example.com/model/schema/folders",
                    "revision": "1169288309"
                }
            }
        }
    }

Get data for a single folder
............................

.. http:get:: /folders/(folder_id)

Retrieve folder details by folder unique identifier.

**Example request**:

.. sourcecode:: http

    GET /folders/{folderId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{folderId}",
            "type": "folders",
            "attributes": {
                "title": "Root Folder"
            },
            "meta": {
                "created": "2018-08-10T10:19:00+00:00",
                "modified": "2018-08-10T10:19:00+00:00",
                "path": "/{folderId}"
            },
            "relationships": {
                "children": {
                    "links": {
                        "related": "https://example.com/folders/{folderId}/children",
                        "self": "https://example.com/folders/{folderId}/relationships/children"
                    }
                },
                "parent": {
                    "links": {
                        "related": "https://example.com/folders/{folderId}/parent",
                        "self": "https://example.com/folders/{folderId}/relationships/parent"
                    }
                }
            }
        },
        "links": {
            "self": "https://example.com/folders/{folderId}",
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

Create a folder
...............

.. http:post:: /folders

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
            "id": "{folderId}",
            "type": "folders",
            "attributes": {
                "uname": "root-folder",
                "title": "Root Folder"
            },
            "meta": {
                "created": "2018-08-10T10:19:00+00:00",
                "modified": "2018-08-10T10:19:00+00:00",
                "path": "/{folderId}"
            },
            "relationships": {
                "children": {
                    "links": {
                        "related": "https://example.com/folders/{folderId}/children",
                        "self": "https://example.com/folders/{folderId}/relationships/children"
                    }
                },
                "parent": {
                    "links": {
                        "related": "https://example.com/folders/{folderId}/parent",
                        "self": "https://example.com/folders/{folderId}/relationships/parent"
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

Modify a folder
...............

.. http:patch:: /folders/(folder_id)


.. sourcecode:: http

    PATCH /folders/{folderId} HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": {
            "id": "{{folderId}}",
            "type": "folders",
            "attributes": {
                "title": "My new folder"
            }
        }
    }

Remove a folder
...............

.. http:delete:: /folders/(folder_id)

Move a folder to trash (*soft delete*) using `DELETE /folders/{folderId}`.

Expected HTTP status response is ``204 No Content`` and an empty body is returned.

**Example request**:

.. sourcecode:: http

    DELETE /folders/{folderId} HTTP/1.1
    Host: example.com

**Example response**:

.. sourcecode:: http

    HTTP/1.1 204 No Content

Folders tree
------------

Get the parent
..............

.. http:get:: /folders/(folder_id)/parent

When a folder is not a root folder (it's a subfolder), parent folder data can be retrieved.
You can obtain data of parent folder, for a specified subfolder, using `GET` with `folder id` followed by `parent`, as in following example.

**Example request**:

.. sourcecode:: http

    GET /folders/{folderId}/parent HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "{folderId}",
            "type": "folders",
            "attributes": {
                "title": "Root Folder"
            },
            "meta": {
                "created": "2018-08-10T10:19:00+00:00",
                "modified": "2018-08-10T12:27:12+00:00",
                "path": "/{folderId}",
                "relation": {
                    "id": {relationId},
                    "object_id": {relatedId},
                    "parent_id": {folderId},
                    "root_id": {folderId},
                    "parent_node_id": 12,
                    "tree_left": 14,
                    "tree_right": 15,
                    "depth_level": 1,
                    "menu": true
                }
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
            "available": "https://example.com/objects?filter%5Btype%5D%5B0%5D=folders",
            "self": "https://example.com/folders/{folderId}/parent",
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

`data.meta.relations` contains the tree details for the folder (*nested set model* has been used to store folders tree data).

.. _folder-set-parent:

Set the parent
..............

.. http:patch:: /folders/(folder_id)/relationships/parent

When you want to set a parent for a folder, you need to call a `PATCH`, specifying the folder identifier in the url and the parent identifier in body payload.

**Example request**:

.. sourcecode:: http

    PATCH /folders/{folderId}/relationships/parent HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": {
            "type": "folders",
            "id": "{parentId}"
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "links": {
            "self": "https://example.com/folders/{folderId}/relationships/parent",
            "home": "https://example.com/home"
        }
    }

Remove the parent
.................

.. http:delete:: /folders/(folder_id)/relationships/parent

When you want a folder be a *root*, you remove its association with the parent.
Removing folder parent association is performed through `DELETE /folders/{folderId}/relationships/parent`,
specifying parent id in the body payload body, like in the :ref:`folder-set-parent`.

Expected HTTP status response is ``204 No Content`` and an empty body is returned.

**Example request**:

.. sourcecode:: http

    DELETE /folders/{folderId}/relationships/parent HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": {
            "type": "folders",
            "id": "{parentId}"
        }
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 204 No Content

Folders contents
----------------

Get contents
............

.. http:get:: /folders/(folder_id)/children

Contents inside a folder are retrieved through `GET /folders/(folder_id)/children`; usual filters, like :ref:`filter-field` or :ref:`filter-search`, are available.

.. _folder-add-content:

Add content
...........

.. http:post:: /folders/{folderId}/relationships/children

You can save contents inside a folder using properly `POST /folders/{folderId}/relationships/children`.
Payload body must contain content object `type` and content identifier `id`, like in the following example.

**Example request**:

.. sourcecode:: http

    POST /folders/{folderId}/relationships/children HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": [
            {
                "type": "{contentTypeName}",
                "id": "{contentId}"
            }
        ]
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

Remove content
..............

.. http:delete:: /folders/{folderId}/relationships/children

Removing contents is performed through `DELETE /folders/{folderId}/relationships/children`.
Payload body must contain content object `type` and content identifier `id`, like in the :ref:`folder-add-content`.

Expected HTTP status response is ``204 No Content`` and an empty body is returned.

**Example request**:

.. sourcecode:: http

    DELETE /folders/{folderId}/relationships/children HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

    {
        "data": [
            {
                "type": "{contentTypeName}",
                "id": "{contentId}"
            }
        ]
    }

**Example response**:

.. sourcecode:: http

    HTTP/1.1 204 No Content
