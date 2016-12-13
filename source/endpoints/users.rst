Users ``/users``
=================

You can manage user data by using ``/users`` endpoint.
It provides users management: creation, data retrieval, modification, removal and roles associations.

Create a user
-------------

You can create user data by using ``POST /users`` endpoint.
Users data must be specified inside body JSON data.

Request body structure is (JSON):

.. sourcecode:: json

    {
        "data": {
            "type": "users",
            "attributes": {
                // ... specificy users field:value data in here ...
                // <field>: <value>,
                // <field>: <value>,
                // ...
            }
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
                "name" : "john",
                "surname" : "doe",
                "company": false,
                "status": "on",
                "uname": "johndoe",
                "locked": false,
                "title": "john",
                "lang": "eng"
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
                "name": "john",
                "surname": "doe",
                "company": false,
                "status": "on",
                "uname": "johndoe",
                "title": "john",
                "lang": "eng"
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

You can obtain user data by using ``GET /users`` and ``GET /users/<id>`` endpoint.

.. http:get:: /users

It returns a collection of users:

    * use ``id`` query string parameter to retrieve a single user by id

.. http:get:: /users/<id>

    **Example request (get users)**:

    .. sourcecode:: http

        GET /users HTTP/1.1
        Host: example.com
        Accept: application/vnd.api+json
        Content-Type: application/x-www-form-urlencoded

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

Modify a user
-------------

You can modify a user by using ``PATCH /users/<id>`` endpoint.

.. http:patch:: /users/<id>

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

You can delete a user by using ``DEL /users/<id>`` endpoint.

.. http:delete:: /users/<id>

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

You can add a role by using ``POST /users/<id>/relationships/roles`` endpoint.
``<id>`` is a placeholder for user object id.
You specify role id inside JSON body passed to request.

.. http:post:: /users/<id>/relationships/roles

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
