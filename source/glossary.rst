Glossary
===========

.. glossary::

    object
        an object in BEdita is the atomic content of your project's data, it could be of different type like a document, an event, an images,
        video or another core type, or it could be a custom type defined specifically in your project

    role
        a role in RBAC permission model (https://en.wikipedia.org/wiki/Role-based_access_control)
        is used to assign permissions to perform some operations

    user
        project users with credentials to login; usually they consist of a username and passowrd or some external
        auth provider identifiers and profile data like first name, surname, contact information etc;
        each user has at least one role, used to grant permissions on some operations;
        user authentication is not always mandatory, some endpoints may respond to GET anonymous requests,
        but to perform _save_ actions a user has to be identified
