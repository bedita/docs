Auhtentication
==============

Key concepts
------------

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

Architecture
------------

The API follow a **token based authentication flow** using a :term:`JSON Web
Token` as :term:`access token` and an opaque token as :term:`refresh token`.

::

    +--------+                                     +---------------+
    |        |--(A)- Authorization Request ------->|   Resource    |
    |        |                                     |     Owner     |
    |        |<-(B)-- Authorization Grant ---------|               |
    |        |                                     +---------------+
    |        |
    |        |                                     +---------------+
    |        |--(C)-- Authorization Grant -------->| Authorization |
    | Client |                                     |     Server    |
    |        |<-(D)----- Access Token (JWT) -------|               |
    |        |                and                  |               |
    |        |           Refresh Token             |               |
    |        |                                     +---------------+
    |        |
    |        |                                     +---------------+
    |        |--(E)----- Access Token (JWT) ------>|    Resource   |
    |        |                                     |     Server    |
    |        |<-(F)--- Protected Resource ---------|               |
    +--------+                                     +---------------+

Usually :term:`JWT` payload contains the user ``id`` and some public claims, for example

.. code-block:: json

    {
        "iss": "https://example.com",
        "iat": "1441749523",
        "exp": "1441707000",
        "id": "15"
    }

.. include:: /fragments/jwt_important.rst

By default all :http:method:`get` requests don't require client and user
authentication unless the object requested has permission on it. In
that case the user has to be authenticated before require the resource.
Other operations as writing/deleting objects (:http:method:`post`, :http:method:`put`, :http:method:`delete` on
``/objects`` endpoint) are always protected instead and they always
require authentication.

All the logic to handle authentication is in ``ApiAuth`` component and
``ApiBaseController`` use it for you so authentication works out of the
box. If you need to protect :ref:`custom endpoints <custom-endpoints>`
you have to add to custom method

.. code-block:: php

    protected function customEndPoint() {
        if (!$this->ApiAuth->identify()) {
            throw new BeditaUnauthorizedException();
        }
    }



Customize authentication
------------------------

If you need to customize or change the authentication you can define
your own auth component. To maintain the component method signature used
in ``ApiBaseController`` your component should implements the interface
``ApiAuthInterface``.

Remember that REST API are thought to implement token based
authentication with the use of both ``access_token`` and
``refresh_token`` so the interface define methods to handle these
tokens. If you need something different probably you would also override
authenication methods of ``ApiBaseController``.

In case you only need some little change it should be better to directly
extend ``ApiAuth`` component that already implements the interface, and
override the methods you need.

For example supposing you want to add additional check to user
credentials, you can simply override ``ApiAuth::authenticate()`` method
which deals with it:

.. code-block:: php

    App::import('Component', 'ApiAuth');

    class CustomAuthComponent extends ApiAuthComponent {

        public function authenticate($username, $password, array $authGroupName = array()) {
            // authentication logic here
        }

    }

and finally to activate the component all you have to do is define in
configuration file ``config/frontend.ini.php`` the auth component you
want to use.

.. code-block:: php

    $config['api'] = array(
        'baseUrl' => '/api',
        'auth' => array(
            'component' => 'CustomAuth'
        )
    );

In ``ApiController`` you will have access to ``CustomAuth`` instance by
``$this->ApiAuth`` attribute.
