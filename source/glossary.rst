Glossary
===========

.. glossary::
    :sorted:

    access token
        A string granted by the authorization server used to identify the issuer of a request.
        The access token has to be sent to the resource server every time that the client wants
        to access protected resources.
        This token is sent in ``Authorization`` HTTP header using a ``Bearer`` scheme on each request like this: ``Authorization: Bearer <token>``


    jwt
    JWT
        JSON Web Tokens are an open, industry standard `RFC 7519 <https://tools.ietf.org/html/rfc7519>`_
        method for representing claims securely between two parties.

        A JWT is composed by three parts:

        * an **header** containing informations about the token type and algorithm used. It is Base64URL encoded.
        * a **payload** containing informations in the form of claims (informations we want to transmit).
          It is Base64URL encoded.
        * a **signature** used to verify the authenticity of the JWT using a valid algorithm defined by
          `JSON Web Signature (JWS) <https://tools.ietf.org/html/rfc7515>`_
          specification (for example a shared secret `HMAC <https://tools.ietf.org/html/rfc7515#page-36>`_).

        More info `here <http://jwt.io>`_.


    object
        An object in BEdita is the atomic content of your project's data, it could be a core types like a document, an event, an image,
        a video or it could be a custom type defined specifically in your project


    project
        A project in BEdita is an independent data set consisting mainly of objects, resources and media files; you may think of a
        database with a set of related resources like media files and configurations; each project will expose its own endpoint to applications


    refresh token
        An opaque token issued by the authorization server. It is useful to renew an expired :term:`access token`
        without send the user credentials again.


    role
        A role in RBAC permission model (https://en.wikipedia.org/wiki/Role-based_access_control)
        is used to assign permissions to perform some operations


    user
        Project users accessing resources with credentials to login; main attributes are username and password or some external
        auth provider identifiers and other profile data like first name, surname, and other contact information;
        each user has usually at least one role, used to grant access on endpoint operations;
        user authentication is not always mandatory, some endpoints may respond to GET anonymous requests,
        but to perform **write** operations a user has to be identified
