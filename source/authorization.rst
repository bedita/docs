******************************
Authentication & Authorization
******************************

BEdita is a multi-application system: it is designed to handle different client applications requests performed by multiple users on the same :term:`project`.

For every request application and user roles should be identified: each application may have its own grants, and each user also depending on :term:`role` assignement.

As a general rule: anonymous read operations are absolutely possibile, for write operations at least the user performing the request must be identified.

To handle these scenarios please have a look at :ref:`configuration-security` configuration and set ``blockAnonymousUsers`` or ``blockAnonymousApps`` flags accordingly.


Authentication
==============

User authentication
-------------------

User authentication is performed using :term:`JWT` standard. You may have a look a the official `JWT introduction <https://jwt.io/introduction/>`_ for a more detailed explanation of the standard.

Basically JWT enables a **token based authentication flow** using an :term:`access token` to access resources
and an opaque :term:`refresh token` to renew the access token.

See :doc:`endpoints/auth` endpoint documentation on how you can retrieve the JWT tokens with a standard username and password user authentication.
Once the token is retrieved it can be used on every request in the ``Authorization`` HTTP header this way:

.. sourcecode:: http

    Authorization: Bearer <token>

Application identification
--------------------------

Client applications instead are identified using a simple **API KEY** mechanism.
Through :doc:`endpoints/admin` endpoint it's possible to set and retrieve an API KEY for each application registered for a project.

On each request you may set the application's API KEY using the ``X-Api-Key`` HTTP header

.. sourcecode:: http

    X-Api-Key: <api-key>

We prefer to talk about application *identification* instead of *authentication* because it's a weak authentication system: web or mobile client applications may expose this api key, or at least secrecy is not guaranteed.
This is somehow similar to ``implicit grant`` in OAuth2.

The only real authentication is *user authentication*: you must rely on that for a **secure** authentication.


Authorization
=============

Once user and application performing a request are identified, also as anonymous, the requested operation may or may not be allowed depending on the outcome of the following checks:

 * **anonymous** user and application settings: depending on :ref:`configuration-security` configuration anonymous requests can be blocked with 401 or 403 responses
 * **endpoint** permissions: each endpoint may allow or deny a request from a user using a client application
 * **object** permissions: at the most granular level a single :term:`object` may or may not be accessible or modifiable to the requesting user and application

Endpoint permissions
--------------------

Operation grants on endpoints can be controlled through a set of rules involving roles, applications and permission types.

Possible values for endpoint, role and application in these rules are:

 * **endpoint** endpoint id or ``NULL`` for every endpoint
 * **role** role id or ``NULL`` for every role or anonymous user
 * **application** application id or ``NULL`` for every application

Instead ``permission type`` may have four different values for read operations (GET) or write operations (POST, PATCH, DELETE):

 * **false** ``(0b00)``: no permissions granted
 * **true** ``(0b11)``: full permissions granted
 * **mine** ``(0b01)``: permissions granted only on **my** resources, i.e. resources belonging to the autenticated user
 * **block** ``(0b10)``: no permissions granted, and override all other permissions

The first two bit, starting from right, are for the read operations (GET) and the other two for the write operations (POST, PATCH, DELETE):

 * ``(0b0000)``: **false** for read [characters `00` on the right] and **false** for write [characters `00` in the middle, shifting by 2 positions]

The relative integer number is used on the ``permission type`` column.

To better understand how these rules work an example is given below:

===========  ==========  =============  ===============================================
  endpoint      role      application                      permission
===========  ==========  =============  ===============================================
 documents     NULL         ios-app      ``int 5  (0b0101)`` write: mine - read: mine
 documents     manager      backend      ``int 15 (0b1111)`` write: true - read: true
 payments       app          NULL        ``int 10 (0b1010)`` write: block - read: block
 events        reader       web-app      ``int 12 (0b1100)`` write: true - read: false
===========  ==========  =============  ===============================================

 * for every role (NULL) on ``/documents`` endpoint through ``ios-app`` application only resources belonging to authenticated user may be read and written
 * users with ``manager`` role accessing ``/documents`` with ``backend`` application are able to read and modify write every resource
 * users with ``app`` role accessing ``/payments`` with every application are not allowed to read or write anything
 * users with ``reader`` role on ``/events`` usgin ``web-app`` application have a read-only access

See :doc:`endpoints/admin` endpoint reference on how you may set these permission rules.

Keep in mind that altough powerful these rules must be applied very carefully like firewall network rules: it is quite easy to cause unintended side effects like blocking every operation or allow dangerous ones.

Object permissions
------------------

This feature is not yet implemented.


OAuth2
======

And what about **OAuth2**?
BEdita is an OAuth2 compliant server solution.
