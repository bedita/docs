Roles ``/roles``
===========================

You can manage user roles by using ``/roles`` endpoint.
It provides user roles management: creation, data retrieval, modification, removal and user association.

Create a role
-------------

You can create role data by using ``POST /roles`` endpoint.
Roles data must be specified inside body JSON data.

Request body structure is:

.. sourcecode:: json

    {
        "data": {
            "type": "roles",
            "attributes": {}
        }
    }

.. http:post:: /roles

**Example request (create sample role)**:

.. sourcecode:: http

    POST /users HTTP/2
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "roles",
            "attributes": {
                "name": "sample role"
            }
        }
    }

Expected response is ``HTTP/2 201 OK``, with ``application/vnd.api+json`` body data representing the role just created.

When role already exists or data is not valid (i.e. data lacks of required fields), POST fails and response is ``400 Bad Request - Invalid data``.

Successful response example follows:

.. sourcecode:: http

    HTTP/2 201 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": "7",
            "type": "roles",
            "attributes": {
                "name": "sample role",
                "created": "2016-12-13T13:38:17+00:00",
                "modified": "2016-12-13T13:38:17+00:00"
            },
            "relationships": {
                "users": {
                    "links": {
                        "related": "http://example.com/roles/1/users",
                        "self": "http://example.com/roles/7/relationships/users"
                    }
                }
            }
        },
        "links": {
            "self": "http://example.com/roles",
            "home": "http://example.com/home"
        }
    }

Get role data
-------------

You can obtain role data by using ``GET /roles`` and ``GET /roles/(role_id)`` endpoint.

.. http:get:: /roles

It returns a collection of roles:

    * use ``id`` query string parameter to retrieve a single role by id
    * use ``id`` query string parameter and ``users`` token to retrieve users by role id

.. http:get:: /roles/(role_id)

**Example request (get roles)**:

.. sourcecode:: http

    GET /roles HTTP/2
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": 7,
                "type": "roles",
                "attributes": {
                    "name": "sample role",
                    "description": null,
                    "unchangeable": false,
                    "created": "2016-12-13T13:38:17+00:00",
                    "modified": "2016-12-13T13:38:17+00:00"
                },
                "links": {
                    "self": "http://example.com/roles/7"
                },
                "relationships": {
                    "users": {
                        "links": {
                            "related": "http://example.com/roles/7/users",
                            "self": "http://example.com/roles/7/relationships/users"
                        }
                    }
                }
            }
        ],
        "links": {
            "self": "http://example.com/roles",
            "home": "http://example.com/home",
            "first": "http://example.com/roles",
            "last": "http://example.com/roles",
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

.. http:get:: /roles/(role_id)/users

You can obtain role users by using ``GET /roles/(role_id)/users`` endpoint.

**Example request (get users by role 'sample role', id 7)**:

.. sourcecode:: http

    GET /roles/7/users HTTP/2
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "19283",
                "type": "users",
                "attributes": {
                    "username": "johndoe",
                },
                "links": {
                    "self": "http://example.com/users/19283"
                },
                "relationships": {
                    "roles": {
                        "links": {
                            "related": "http://example.com/users/19283/roles",
                            "self": "http://example.com/users/19283/relationships/roles"
                        }
                    }
                }
            }
        ],
        "links": {
            "self": "http://example.com/roles/7/users",
            "home": "http://example.com/home",
            "first": "http://example.com/roles/7/users",
            "last": "http://example.com/roles/7/users",
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

Modify a role
-------------

You can modify a role by using ``PATCH /roles/(role_id)`` endpoint.

.. http:patch:: /roles

**Example request (modify role 'sample role')**:

In this example, purpose is modifying 'sample role' name to 'Dummy Role'.

.. sourcecode:: http

    PATCH /roles/7 HTTP/2
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 7,
            "type": "roles",
            "attributes": {
                "name" : "Dummy Role"
            }
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
           "id": 7,
            "type": "roles",
            "attributes": {
                "name": "Dummy Role",
                "description": null,
                "unchangeable": false,
                "created": "2016-12-13T13:38:17+00:00",
                "modified": "2016-12-13T14:02:37+00:00"
            },
            "relationships": {
                "users": {
                    "links": {
                        "related": "http://example.com/roles/7/users",
                        "self": "http://example.com/roles/7/relationships/users"
                    }
                }
            }
        },
        "links": {
            "self": "http://example.com/roles/7",
            "home": "http://example.com/home"
        }
    }

Remove a role
-------------

You can delete a role by using ``DEL /roles/(role_id)`` endpoint.

.. http:delete:: /roles

**Example request (delete role 'Sample Role', id 7)**:

.. sourcecode:: http

    DELETE /roles/7 HTTP/2
    Host: example.com

Expected response is ``204 No Content``. When role is not found, response is ``404 Not Found``.

.. sourcecode:: http

    HTTP/2 204 No Content

Add a user role
---------------

You can add a role to a user by using ``POST /roles/(role_id)/relationships/users`` endpoint.
``(role_id)`` is a placeholder for role id.
You specify user id inside JSON body passed to request.

.. http:post:: /roles/(role_id)/relationships/users

**Example request (add role 7 to john doe user, id 19283)**:

In this example, purpose is adding a role (id 7) to 'johndoe' user (id 19283).

.. sourcecode:: http

    POST /roles/7/relationships/users HTTP/2
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "users",
            "id": 19283
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "links": {
            "self": "http://example.com/roles/7/relationships/users",
            "home": "http://example.com/home"
        }
    }

Remove a user role
------------------

You can remove a role from a user by using ``DELETE /roles/(role_id)/relationships/users`` endpoint.
``(role_id)`` is a placeholder for role id.
You specify user id inside JSON body passed to request.

.. http:delete:: /roles/(role_id)/relationships/users

**Example request (remove role 7 from john doe user, id 19283)**:

In this example, purpose is removing a role (id 7) from 'johndoe' user (id 19283).

.. sourcecode:: http

    DELETE /roles/7/relationships/users HTTP/2
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "users",
            "id": 19283
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/2 200 OK
    Content-Type: application/vnd.api+json

    {
        "links": {
            "self": "http://example.com/roles/7/relationships/users",
            "home": "http://example.com/home"
        }
    }
