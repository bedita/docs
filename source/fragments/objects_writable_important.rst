.. important::

    To write an object it has to be :doc:`configured to be writable </configuration>`

    .. code-block:: php

        $config['api'] = array(
            // ....
            'validation' => array(
                // to save 'document' and 'event' object types
                'writableObjects' => array('document', 'event')
            )
        );
