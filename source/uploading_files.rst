Uploading files
===============

The BEdita REST API allows to upload files and link it to BEdita objects.
The process of upload follow the schema:

::

    +--------+                                                   +---------+
    |        |--(A)-- POST /files/:object_type/:filename ------->|         |
    |        |                                                   |         |
    |        |<-(B)------------- 200 Success --------------------|         |
    |        |             {"upload_token": "hash"}              |         |
    | Client |                                                   |   API   |
    |        |                                                   |         |
    |        |--(C)----- POST /objects with upload_token ------->|         |
    |        |                                                   |         |
    |        |<-(D)------------- 201 Created --------------------|         |
    +--------+                                                   +---------+

where:

(A) The client send a request to upload a file named `:filename` related to an object type `:object_type`.
    See :http:post:`/files/(string:object_type)/(string:file_name)` for more info.
(B) The API responds with :http:statuscode:`200` and an :term:`upload token`.
(C) The client finalize the upload request creating a BEdita object
    and using the :term:`upload token` received for link the new object to
    the file uploaded
(D) The API responds with  :http:statuscode:`201` as shown in :http:post:`/objects`

Limiting which mime types are allowed
-------------------------------------

If you want to limit the mime types of files uploadable you can edit the frontend configuration
in ``app/config/frontend.cfg.php`` defining for every object type an array of regexp used to validate the mime type.
If no one is defined the rules that you can find in ``bedita-app/config/bedita.ini.php`` will be applied.

An example of configuration could be:

.. code-block:: php

    $config['validate_resource']['mime']['Image'] = array('/image\/png/', '/image\/gif/');
    $config['validate_resource']['mime']['Video'] = array('/video\/\.*/');

To allow ``image/png`` and ``image/gif`` mime types for ``Image`` object
and all ``video/*`` mime types for ``Video`` object.

.. _quota-limits:

Limiting max file size allowed and quota available
--------------------------------------------------

Usually you would want to limit the amount of files and space available for every user. 
The default max file size supported and the quota available are defined in ``ApiUploadComponent`` as

.. code-block:: php

    protected $quota = array(
        'maxFileSize' => 52428800,  // max file size uploadable 50 MB
        'maxSizeAvailable' => 524288000,  // quota available 500 MB
        'maxFilesAllowed' => 500 // max number of files uploadable
    );

These parameters are configurable editing ``app/config/frontend.cfg.php``, for example

.. code-block:: php

    $config['api']['upload'] => array(
        'quota' => array(
            'maxFileSize' => 8*1024*1024, // 8 MB
            'maxSizeAvailable' => 50*1024*1024, // 50 MB
            'maxFilesAllowed' => 100
        )
    );

Creating a custom object type supporting upload
-----------------------------------------------

If you want to use ``/files`` endpoint for your custom object types, your model must follow some conventions:
 
* it can **extends** ``BeditaSimpleStreamModel`` or ``BeditaStreamModel``
  as done by core multimedia ``object_type`` (image, video, audio, b_e_file, application).

  In this case the upload will follow the classic BEdita flow putting the file uploaded in the path defined in configuration
  as ``$config['mediaRoot']`` and reachable from ``$config['mediaUrl']`` url unless you follow the below convention.

* if you need to handle the upload in a way different the object model must **implements** two methods:

 .. php:method:: apiUpload(File $source, array $options)

      Used for execute the upload action given the source file. It deals with moving the uploaded file
      in a target destination returning the target url.

      :param File $source: The instance of CakePHP `File <http://api.cakephp.org/1.3/class-File.html>`_ referred to the
                           file uploaded
      :param array $options: An array of options containing:
    
                             * ``fileName``: the safe file name obtained from source original file name
                             * ``hashFile``: the md5 hash of the source file
                             * ``user``: the user data identified by :term:`access token`
      :returns: Either false on failure, or the target url where the the file is reachable.

 .. php:method:: apiUploadTransformData(array $uploadData)

      Used for finalize the upload action adding additional fields to object data to save.

      :param array $uploadData: An array of data containing information about file previously uploaded:

                             * ``uri``: the url of file uploaded (that one returned from ``apiUpload()``)
                             * ``name``: the safe file name
                             * ``mime_type``: the file mime-type
                             * ``file_size``:  the file size
                             * ``original_name``:  the original file name uploaded
                             * ``hash_file``: the md5 hash of file
      :returns: array of additional fields to add to object data passed in creation according to ``Model`` table used.
