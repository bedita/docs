Users ``/users``
=================

.. _api-users:

You can manage user data by using ``/users`` endpoint.
It provides users management: creation, data retrieval, modification, removal and roles associations.

Create a user
-------------

You can create user data by using ``POST /users`` endpoint.
Users data must be specified inside body JSON data.

Request body structure is:

.. sourcecode:: json

    {
        "data": {
            "type": "users",
            "attributes": {}
        }
    }

.. http:post:: /users

**Example request (create user john doe)**:

.. sourcecode:: http

    POST /users HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "users",
            "attributes": {
                "username": "johndoe",
                "password": "j04nd0e",
                "uname": "johndoe"
            }
        }
    }

Example above show all required attributes.

Expected response is ``HTTP/1.1 201 OK``, with ``application/vnd.api+json`` body data representing the user just created.

When user already exists or data is not valid (i.e. data lacks of required fields), POST fails and response is ``400 Bad Request - Invalid data``.

Successful response example follows:

.. sourcecode:: http

    HTTP/1.1 201 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 19283,
            "type": "users",
            "attributes": {
                "username": "johndoe",
                "password": "j04nd0e",
                "uname": "johndoe",
                "created_by": 1,
                "modified_by": 1,
                "created": "2016-12-13T12:08:02+00:00",
                "modified": "2016-12-13T12:08:02+00:00"
            },
            "relationships": {
                "roles": {
                    "links": {
                        "related": "http://example.com/users/19283/roles",
                        "self": "http://example.com/users/19283/relationships/roles"
                    }
                }
            }
        },
        "links": {
            "self": "http://example.com/users",
            "home": "http://example.com/home"
        }
    }

``data.attributes`` object contains more internal attributes.

Get user data
-------------

You can obtain user data by using ``GET /users`` and ``GET /users/(user_id)`` endpoint.

.. http:get:: /users

It returns a collection of users:

    * use ``id`` query string parameter to retrieve a single user by id
    * use ``id`` query string parameter and ``roles`` token to retrieve user roles by user id

.. http:get:: /users/(user_id)

**Example request (get users)**:

.. sourcecode:: http

    GET /users HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": 19283,
                "type": "users",
                "attributes": {
                    "username": "johndoe",
                    "name": "john",
                    "surname": "doe"
                }
            }
        ],
        "links": {
            "self": "http://example.com/users/19283",
            "home": "http://example.com/home",
            "first": "http://example.com/users",
            "last": "http://example.com/users",
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

``data`` is an array of objects; in this example, you see only one.
``data.attributes`` object contains more internal attributes.

.. http:get:: /users/(user_id)/roles

You can obtain user roles by using ``GET /users/(user_id)/roles`` endpoint.

**Example request (get user johndoe roles)**:

.. sourcecode:: http

    GET /users/19283/roles HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json

**Example response**:

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": [
            {
                "id": "7",
                "type": "roles",
                "attributes": {
                    "name": "dummy role",
                    "description": null,
                    "unchangeable": false,
                    "created": "2016-12-13T11:28:32+00:00",
                    "modified": "2016-12-13T11:28:32+00:00"
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
            "self": "http://example.com/users/19283/roles",
            "home": "http://example.com/home",
            "first": "http://example.com/users/19283/roles",
            "last": "http://example.com/users/19283/roles",
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

Modify a user
-------------

You can modify a user by using ``PATCH /users/(user_id)`` endpoint.

.. http:patch:: /users/(user_id)

**Example request (modify user john doe)**:

In this example, purpose is modifying 'johndoe' user's name and surname from 'john doe' to 'Johnny Doe'.

.. sourcecode:: http

    PATCH /users/19283 HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 19283,
            "type": "users",
            "attributes": {
                "name" : "Johnny",
                "surname" : "Doe"
            }
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "data": {
            "id": 19283,
            "type": "users",
            "attributes": {
                "username": "johndoe",
                "name": "Johnny",
                "surname": "Doe"
            },
            "relationships": {
                "roles": {
                    "links": {
                        "related": "http://example.com/users/19283/roles",
                        "self": "http://example.com/users/19283/relationships/roles"
                    }
                }
            }
        },
        "links": {
            "self": "http://example.com/users/19283",
            "home": "http://example.com/home"
        }
    }

``data.attributes`` object contains more internal attributes.

Remove a user
-------------

You can delete a user by using ``DEL /users/(user_id)`` endpoint.

.. http:delete:: /users/(user_id)

**Example request (delete user john doe)**:

Note: in this example user id is 19283.

.. sourcecode:: http

    DELETE /users/19283 HTTP/1.1
    Host: example.com

Expected response is ``204 No Content``. When user is not found, response is ``404 Not Found``.

.. sourcecode:: http

    HTTP/1.1 204 No Content

Add a role
----------

You can add a role by using ``POST /users/(user_id)/relationships/roles`` endpoint.
``(user_id)`` is a placeholder for user object id.
You specify role id inside JSON body passed to request.

.. http:post:: /users/(user_id)/relationships/roles

**Example request (add role 7 to john doe user)**:

In this example, purpose is adding a role (id 7) to 'johndoe' user (id 19283).

.. sourcecode:: http

    POST /users/19283/relationships/roles HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "roles",
            "id": 7
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "links": {
            "self": "http://example.com/users/19283/relationships/roles",
            "home": "http://example.com/home"
        }
    }

Remove a role
-------------

You can remove a role by using ``DELETE /users/(user_id)/relationships/roles`` endpoint.
``(user_id)`` is a placeholder for user object id.
You specify role id inside JSON body passed to request.

.. http:delete:: /users/(user_id)/relationships/roles

**Example request (remove role 7 to john doe user)**:

In this example, purpose is removing a role (id 7) from 'johndoe' user (id 19283).

.. sourcecode:: http

    DELETE /users/19283/relationships/roles HTTP/1.1
    Host: example.com
    Accept: application/vnd.api+json
    Content-Type: application/vnd.api+json

    {
        "data": {
            "type": "roles",
            "id": 7
        }
    }

Response 200 OK is expected.

.. sourcecode:: http

    HTTP/1.1 200 OK
    Content-Type: application/vnd.api+json

    {
        "links": {
            "self": "http://example.com/users/19283/relationships/roles",
            "home": "http://example.com/home"
        }
    }
