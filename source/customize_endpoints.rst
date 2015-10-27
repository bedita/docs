Customize endpoints
===================

.. _custom-endpoints:

Custom endpoints
----------------

Once you have :doc:`enabled a frontend to consume API </setup>`
you have a set of :doc:`default available endpoints </endpoints/index>`
visible pointing the browser to your API base url.

Sometimes you would want to define other endpoints to serve your custom
data to clients. You can do it simply override the ``$endpoints``
attribute of ``ApiBaseController``.

Write in your ``ApiController``

.. code-block:: php

    protected $endPoints = array('friends');

and define the related custom method that will handle the data to show

.. code-block:: php

    protected function friends() {
        $friendsList = array('Tom', 'Jerry');
        $this->setData($friendsList);
    }

The ``setData()`` method takes care of put ``$friendsList`` array inside
response ``data`` key. Point the browser to your API base url you should
see 'friends' in the endpoints list and if you request
``GET /api/base/url/friends`` you should see

.. code-block:: json

    {
        "api": "friends",
        "data": [
            "Tom",
            "Jerry"
        ],
        "method": "get",
        "params": [],
        "url": "https://example.com/api/v1/friends"
    }

In this way all request types (GET, POST, PUT, DELETE) have to be
handled by ``friends()`` method. Another possibility is to create one
method for every request type allowed from the endpoint. It can be done
creating methods named *"request type + endpoint camelized"*.

.. code-block:: php

    protected function getFriends() {
    }

    protected function postFriends() {
    }

    protected function putFriends() {
    }

    protected function deleteFriends() {
    }


Blacklist endpoints
-------------------

In some situations you will not want to expose some or all default
endpoints, so in order to disable them it is possible to define a
blacklist. After that calling those endpoints the response will be a
**405 Method Not Allowed** HTTP error status code.

For example to blacklist "auth" and "objects" endpoints, in your
``ApiController`` override ``$blacklistEndPoints`` writing

.. code-block:: php

    protected $blacklistEndPoints = array('auth', 'objects');

Now, pointing to API base url you shouldn't see "auth" and "objects" endpoints anymore.

Pointing to them directly and you will receive a **405 HTTP error**.


Enable special object types endpoints
-------------------------------------

If you need you can also enable some special endpoint disabled by
default. Those endpoints refer to BEdita object types mapping them to
their pluralize form. So if you want to enable ``/documents`` end
``/galleries`` endpoints you have to edit ``ApiController``

.. code-block:: php

    protected $whitelistObjectTypes = array('document', 'gallery');

These special endpoints automatically filter response objects through
the object type related.

Again go to API base url to see 'documents' and 'galleries' added to
endpoints list.

   .. note::

      Note that those special endpoints work only for GET requests.


Customize ``/objects`` endpoint with your own URL path filter types
-------------------------------------------------------------------

``objects`` endpoint can be customized with URL path filters building
endpoint structured as ``/objects/:id/url_path_filter``. URL path
filters on by default are visible in ``ApiBaseController::$allowedObjectsUrlPath`` property

.. code-block:: php

    protected $allowedObjectsUrlPath = array(
            'get' => array(
                'relations',
                'children',
                'contents',
                'sections',
                'descendants',
                'siblings',
                //'ancestors',
                //'parents'
            ),
            'post' => array(
                'relations',
                'children'
            ),
            'put' => array(
                'relations',
                'children'
            ),
            'delete' => array(
                'relations',
                'children'
            )
        );

URL path filters can be inhibited or new ones can be added overriding
that property in ``ApiController``.

In practice URL path filters are divided by request type (GET, POST,
...) so it is possible doing request like ``GET /objects/1/children``,
``POST /objects/1/relations`` but not ``POST /objects/1/siblings``
because of that filter is active only for GET requests.

Every URL path filter must have a corresponding controller method named
*"request type + Objects + URL path filter camelized"* that will handle
the request. First url part *:id* and every other url parts after URL
path filter will be passed to that method as arguments.

For example, supposing to want to remove all 'delete' and 'post' URL
path filters and add a new 'foo\_bar' filter for GET request, in
``ApiController`` we can override

.. code-block:: php

    protected $allowedObjectsUrlPath = array(
            'get' => array(
                'relations',
                'children',
                'contents',
                'sections',
                'descendants',
                'siblings',
                'foo_bar'
            ),
        );

and add the method

.. code-block:: php

    protected function getObjectsFooBar($objectId) {
        // handle request here
    }

In this way the new URL path filter is active and reachable from
``GET /objects/:id/foo_bar``. Every other request type (POST, PUT,
DELETE) to that will receive **405 Method Not Allowed**.

If our 'foo\_bar' URL path filter have to support
``GET /objects/:id/foo_bar/:foo_val`` requests then
``ApiController::getObjectsFooBar()`` will receive ``:foo_val`` as
second argument. A best practice should be to add to method a validation
on the number of arguments supported to avoid to respond to request as
``GET /objects/:id/foo_bar/:foo_val/bla/bla/bla``.

.. code-block:: php

    protected function getObjectsFooBar($objectId, $fooVal = null) {
        if (func_num_args() > 2) {
            throw new BeditaBadRequestException();
        }
        // handle request here
    }
