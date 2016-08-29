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
