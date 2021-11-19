Request and Response
====================

Every **BEdita 4** API call provides a standard request and response format layout: most of the endpoints are operations involving resources, think of classical CRUD operations for simplicity, and will have the same layout while some other operations like authentication are not and are slightly different.

Requests and responses will follow in general the :term:`JSON API` specification as default format.
There are few exceptions though: in some situations a very simple JSON object format is used as request body. See below for some examples.


.. _api-methods:

Methods
-------

**HTTP** methods used in API requests, following common REST best practices, are:

 * **GET** to read resources or other meta informations
 * **POST** to create new resources or for operations requiring input data like authentication
 * **PATCH** to modify existing resources
 * **DELETE** to delete existing resources

.. _api-requests:

Request
-------

**DELETE** and **GET** requests do not contain in general a body. Have a look at :doc:`parameters` for an overview of GET query strings.

In case of **POST** and **PATCH** methods instead a :term:`JSON API` compliant format body part has to be sent by the client.

A minimal body request will have this layout:

   .. sourcecode:: json

        {
            "data": {
                "type": "documents",
                "attributes": {
                    "title": "This is a title",
                    "description": "Brief description or abstract",
                    "body": "Document body with long text..."
                }
            }
        }

Very few operations like authentication require a flat key-value JSON object format like:

    .. sourcecode:: json

        {
            "username": "my-username",
            "password": "my-password"
        }


.. _api-responses:


Response
--------

Everytime a response contains a body it will be in :term:`JSON API` compliant format.

A simple example of a **GET /documents** response will help us highlight some important concepts:

   .. sourcecode:: json

        {
            "data": [
                {
                    "id": "2",
                    "type": "documents",
                    "attributes": {
                        "title": "My first document",
                        "description": "A brief description",
                        "body": "A long description",
                        "status": "draft",
                        "uname": "documents_my-first-document",
                        "custom_field": 1234,
                        "extra": {
                            "ke1": "value1",
                            "key2": [
                                "value2",
                                "other value"
                            ]
                        },
                        "lang": "en"
                    },
                    "meta": {
                        "locked": false,
                        "created": "2017-03-22T17:39:23+00:00",
                        "modified": "2017-06-25T21:33:33+00:00",
                        "published": null,
                        "created_by": 3,
                        "modified_by": 9
                    },
                    "links": {
                        "self": "https://api.example.com/documents/2"
                    },
                    "relationships": {
                        "linked_to": {
                            "links": {
                                "related": "https://api.example.com/documents/2/linked_to",
                                "self": "https://api.example.com/documents/2/relationships/linked_to"
                            }
                        }
                    }
                },
                {
                    "id": "3",
                    "type": "documents",
                    "attributes": {
                    }
                },
                {
                }
            ],
            "links": {
                "self": "https://api.example.com/documents",
                "home": "https://api.example.com/home",
                "first": "https://api.example.com/documents",
                "last": "https://api.example.com/documents&page=5",
                "prev": null,
                "next": "https://api.example.com/documents&page=2"
            },
            "meta": {
                "pagination": {
                    "count": 92,
                    "page": 1,
                    "page_count": 5,
                    "page_items": 20,
                    "page_size": 20
                }
            }
        }

This is the classical response in case of resources list. For a single resource response is very similar, but somehow reduced.

At the root level we have:

 * ``"data"`` key will contain an array of resources, or a single resource when a single resource is requested like in **GET /documents/2**
 * ``"meta"`` will contain metadata of single resources or of resource lists, mainly pagination info for lists
 * ``"links"`` key appears in more than one place and will contain

    - ``"self"`` (always present) with a link pointing to the resource containing it; it can be an array of resources, a single resource or a relationship
    - ``"home"`` link to :doc:`home` endpoint
    - ``"first"`` ``"last"`` ``"prev"`` ``"next"`` :ref:`get-pagination` URIs to access pages in case of resources array

Giving a closer look at **data** key there are some important things to notice:

 * ``"id"`` and ``"type"`` must always be present, ``"type"`` is the unique plural name of object types (like `documents` in this case) or of internal resources (like `roles`)
 * ``"attributes"`` is for resource attributes, these are generally properties that a client application can modify, once permissions are granted, and contains

    - **stantard core** properties like ``"title"``, ``"description"``, ``"lang"`` or ``"body"`` available for all object types
    - **custom** properties that belong to the current resource or object type only - ``"custom_field"`` in this example
    - **special core** properties available for all object types, with some specific meaning and usage:

        * ``"status"`` may have only ``on``, ``draft`` or ``off`` as values; it representes an editorial status, depending on client application settings objects with status ``draft`` and ``off`` may not be visible
        * ``"uname"`` is the object unique name, a modifiable unique identifier of every object inside a project, represented by an URL-friendly string that may be derived from the ``"title"`` or from other properties; upon creation a ``uname`` is automatically created and when a client application requests to create or modifiy this property the actual value may be changed by the system to avoid conflicts with an existing identical ``uname``
        * ``"extra"`` is a special property where an application may put arbitrary data, use it as you wish... we don't wanna know :)

 * ``"meta"`` is for resource metadata, or properties that are not directly changeable by a client application, like:

    - ``"created"``, ``"modified"`` and ``"published"`` will show creation date, last modification date and date of publishing (when ``status`` changed to ``on``)
    - ``"created_by"`` and ``"modified_by"`` display the id of the user that created the object and of the user that modified it for the last time
    - ``"locked"`` is a special property, when set to **true** an object is not deletable and its ``status`` and ``uname`` cannot be modified

 * ``"links"`` contains a ``"self"`` link to representation of the object itself
 * ``"relationships"`` displays links to read or manipulate related resources or objects, in general we have:
    * **core** relations between objects and resources, like the one between :term:`user` and :term:`role`
    * **custom** semantic relations between objects dynamically created using :doc:`model/relations`

.. _api-http-status:

Status codes
------------

Obviously on every response a meaningful `HTTP status code <https://en.wikipedia.org/wiki/List_of_HTTP_status_codes>` is returned.
The API consumer must always check it before reading the response body: especially to see if an error has occured.

Here a short list of the main status codes your client application will receive:

 * ``200 OK`` - A successful GET or PATCH or POST that doesn't result in a creation.
 * ``201 Created`` - Successful POST that results in a creation. A Location header pointing to the location of the new resource will be provided.
 * ``204 No Content`` - Successful not returning a body (like a DELETE).
 * ``400 Bad Request`` - The request is malformed.
 * ``401 Unauthorized`` - No authentication or invalid authentication details are provided.
 * ``403 Forbidden`` - Authentication succeeded but authenticated user doesn't have access to the resource.
 * ``404 Not Found`` - A non-existent resource is requested.
 * ``405 Method Not Allowed`` - An HTTP method is being requested that isn't allowed (like requesting a DELETE on :doc:`home`).
 * ``406 Not Acceptable`` - A content negotiation error, content generated is not acceptable according to the `Accept` headers sent in the request - see :ref:`api-headers`.
 * ``415 Unsupported Media Type`` - An unsupported or unknown content type was provided as part of the request.
 * ``500 Internal Server Error`` - Something bad happened, hope you don't get a lot of them :)

.. _api-errors:

Errors
------

In case of error this is the expected response body layout

   .. sourcecode:: json

        {
            "error": {
                "status": "401",
                "title": "Unauthorized",
                "code": "expired_token",
                "detail": "JWT access token has expired",
                "meta": {
                    "trace": [
                        "#0 /var/www/bedita/plugins/BEdita/API/src/Auth/EndpointAuthorize.php(133): BEdita\\API\\Auth\\JwtAuthenticate->unauthenticated(Object(Cake\\Http\\ServerRequest), Object(Cake\\Http\\Response))",
                        "#1 /var/www/bedita/plugins/BEdita/API/src/Auth/EndpointAuthorize.php(115): BEdita\\API\\Auth\\EndpointAuthorize->unauthenticate()",
                        "#2 ...",
                        "....",
                        "#27 {main}"
                    ]
                }
            },
            "links": {
                "self": "https://api.example.com/documents",
                "home": "https://api.example.com/home"
            }
        }

Main difference between a failure and a successful response is the presence of the ``"error"`` key providing:

 * ``"status"`` response HTTP status code, for convenience
 * ``"title"`` short, human-readable summary of the problem that SHOULD NOT change from occurrence to occurrence of the problem
 * ``"code"`` API specific error code in string format, used by API consumer to better identify the problem occurred
 * ``"detail"`` human-readable explanation specific to this occurrence of the problem. Can be localized.
 * ``"meta"`` will contain stacktrace in ``"trace"`` only in **debug** mode
 * ``"links"`` with ``"self"`` URL where error originated, and ``"home"`` URL
