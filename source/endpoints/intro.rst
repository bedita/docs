Introduction
============

**BEdita 4** exposes a set of default endpoints to model and handle your data and to perform many other
tyipical backend API operations. Throughout this documentation a description of each of these **core** endpoints is given.

All API examples are conventionally described using only **HTTP** requests and responses i.e. methods, url, headers and bodies.
There are intentionally no API client language specific examples, so a basic knowledge of REST API concepts and HTTP is necessary.

.. _api-headers:

Headers
-------

Since the primary exchange format is JSON-API its `content negotiation rules <http://jsonapi.org/format/#content-negotiation>`_
are applied with some extensions.

Every request must contain an ``Accept`` header with one of the following values:

    * ``application/vnd.api+json`` JSON-API default content type
    * ``application/json`` is also fine; considered a synonym for JSON-API, is treated the same way

Under special circumstances, and only for GET requests, also ``text/html``, ``application/xhtml+xml``, ``application/xhtml``
and ``text/xhtml`` may be valid, provided that HTML accept is configured (see :ref:`configuration-accept`):
response will be an HTML version of the JSON body.
Main use is enable data navigation using a browser only in test and development enviroments.

Request containing input data like POST, PATCH and in some cases DELETE must also provide a ``Content-Type`` header
where accepted values are:

    * ``application/vnd.api+json`` JSON-API default content type
    * ``application/json`` is considered a synonym for JSON-API just like ``Accept``

**Note**: some special requests like ``POST /auth`` will require ``application/json`` since not JSON-API compliant,
see ref:`auth-login`.

Other two important headers are:
   * ``Authorization`` containing `JWT <https://jwt.io>`_ token in the form ``Bearer {token}`` to identify the user performing the request
   * ``X-Api-Key`` containing a token to identify the application performing the request

Typical requests will then be like:

    .. sourcecode:: http

        POST /users HTTP/1.1
        Accept: application/vnd.api+json
        Content-Type: application/vnd.api+json
        Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9......

    .. sourcecode:: http

        GET /users/123 HTTP/1.1
        Accept: application/json
        X-Api-Key: WF0IjoxNDk1MTgzMjgzLCJuYmYiOjE0OTUxODMyODMsInN1


.. _api-dynamic-endpoints:


Dynamic endpoints
-----------------

Every :term:`object` type in BEdita defines a new endpoint dynamically, so there will be more ``dynamic`` endpoints
in a typical project setup than the ones described here.

Some core object types like ``documents`` or ``profiles`` already provide their endpoints namely ``/documents`` and ``/profiles``
where through GET/POST/PATCH/DELETE methods you will be able to manage their data in the same way you can do in :ref:`api-users`.

The only difference will be data structure and relations between objects that will of course vary from type to type hence
from endpoint to endpoint.

Same will happen for new object types created dynamically in :ref:`api-object_types`: a new dedicated endpoint is created,
so a new *cats* object type will automatically have its own ``/cats`` endpoint.
