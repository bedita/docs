Glossary
========

.. glossary::
    :sorted:

    Access token
        A string granted by the authorization server used to identify the issuer of a request.
        The access token has to be sent to the resource server every time that the client want
        to access to protected resources.

        BEdita REST API uses :term:`JSON Web Token` as access token.
        It can be sent as ``Authorization`` HTTP header (preferred) using a ``Bearer`` scheme

        ::

            Authorization: Bearer <token>

        or as query string ``/endpoint?access_token=<token>``

    Refresh token
        An opaque token issued by the authorization server. It is useful to renew an expired :term:`access token`
        without send the user credentials again. This token doesn't expire but can be revoked by
        :http:delete:`/auth/(string:refresh_token)`

    JSON Web Token
    JWT
        JSON Web Tokens are an open, industry standard `RFC 7519 <https://tools.ietf.org/html/rfc7519>`_
        method for representing claims securely between two parties.

        A JWT is composed by three parts:

        * an **header** containing informations about the token type and algorithm used. It is Base64URL encoded.
        * a **payload** containing informations in the form of claims (informations we want to transmit).
          It is Base64URL encoded.
        * a **signature** used to verify the authenticity of the JWT using an valid algorithm defined by
          `JSON Web Signature (JWS) <https://tools.ietf.org/html/rfc7515>`_
          specification (for example a shared secret `HMAC <https://tools.ietf.org/html/rfc7515#page-36>`_).

        More info `here <http://jwt.io>`_.

    Upload token
        An opaque token issued from API server that refers to a file uploaded
        from a client with an authenticated user and for a specific object type.
        It is used to link an uploaded file to a BEdita object as shown in :doc:`/uploading_files` section.

        The token has a an expiration date. If used after that expiration date
        the request will be invalid. 