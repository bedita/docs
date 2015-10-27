.. important::

    Because of :term:`JWT` is digital signed using ``'Security.salt'`` you should
    always remember to change it in ``app/config/core.php`` file:

    .. code-block:: php

        Configure::write('Security.salt', 'my-security-random-string');

    It is possible to invalidate all :term:`access token` released simply
    changing that value.
