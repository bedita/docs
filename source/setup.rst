Setup a frontend to consume API
===============================

To use REST API in your frontend app you need at least BEdita 3.6.0
version. You can already also test it using 3-corylus branch.

.. note::

   Because of authentication is handled using `Json Web Token <http://jwt.io>`_ (`IETF <https://tools.ietf.org/html/rfc7519>`_)
   and the JWT is digital signed using ``'Security.salt'`` you should always remember to change
   it in ``app/config/core.php`` file:

   .. code-block:: php

      Configure::write('Security.salt', 'my-security-random-string');

Enable API on new frontend app
------------------------------

- from shell

   .. code-block:: shell

      cd /path/to/bedita
      ./cake.sh frontend init

- in ``app/config/frontend.ini.php`` define ``$config['api']['baseUrl']`` with your API base url, for example

   .. code-block:: php

      $config['api'] = array('baseUrl' => '/api/v1');

That's all! You are ready to consume the API!

Point the browser to your API base url and you should see the list of
endpoints available, for example

.. code-block:: json

    {
        "auth": "https://example.com/api/v1/auth",
        "me": "https://example.com/api/v1/me",
        "objects": "https://example.com/api/v1/objects",
        "posters": "https://example.com/api/v1/posters"
    }


Enable API on old frontend app
------------------------------

- create a new ``ApiController`` in your frontend

   .. code-block:: php

      require(BEDITA_CORE_PATH . DS . 'controllers' . DS . 'api_base_controller.php');

      class ApiController extends ApiBaseController {
         //...
      }

- in ``app/config/frontend.ini.php`` define ``$config['api']['baseUrl']`` with your API base url.

- edit ``app/config/routes.php`` putting

   .. code-block:: php

      $apiBaseUrl = Configure::read('api.baseUrl');
      if (!empty($apiBaseUrl) && is_string($apiBaseUrl)) {
         Router::connect($apiBaseUrl . '/*', array('controller' => 'api', 'action' => 'route'));
      }

   above

   .. code-block:: php

      Router::connect('/*', array('controller' => 'pages', 'action' => 'route'));

That's all!

After `#570 <https://github.com/bedita/bedita/issues/570>`__ we have
implemented a new (and better) way to handle Exceptions. Remember to
update your frontend ``index.php`` file:

.. code-block:: php

    if (isset($_GET['url']) && $_GET['url'] === 'favicon.ico') {
        return;
    } else {
        $Dispatcher = new Dispatcher();
        $Dispatcher->dispatch();
    }

Also make sure you have defined ``views/errors/error.tpl`` in your
frontend for generic error handling.
