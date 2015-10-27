Pagination
==========

Requesting a list of objects by ``/objects`` endpoint the result will be
paginated using default values that you can :ref:`customize <pagination-customize>` in ``ApiController``.

In the response you'll see tha pagination data in ``paging`` key

.. code-block:: json

    {
        "api": "objects",
        "data": {},
        "method": "get",
        "paging": {
            "page": 1,
            "page_size": 10,
            "page_count": 10,
            "total": 995,
            "total_pages": 100
        },
        "params": [],
        "url": "https://example.com/api/v1/objects/1/children"
    }

where

-  ``page`` is the current page
-  ``page_size`` is the items per page
-  ``page_count`` is the count of items in current page
-  ``total`` is the total items
-  ``total_pages`` is the total numbers of pages

To request a specific page simply call the endpoint passing ``page`` as
GET parameter for example ``/api/objects/1/children?page=5`` to request
the page 5.

You can also change the page size always through GET parameter, for
example ``/api/objects/1/children?page_size=50`` to request 50 objects
per page. ``page_size`` can't be greater of ``$paginationOptions['maxPageSize']`` defined in controller.

See below to know how to change the default values.

.. _pagination-customize:

Define your API pagination default options
------------------------------------------

The default values used paginating items are defined in
``ApiBaseController::paginationOptions`` property.

.. code-block:: php

    protected $paginationOptions = array(
        'page' => 1,
        'pageSize' => 20,
        'maxPageSize' => 100
    );

where ``pageSize`` is the default items per page and ``maxPageSize`` is
the max page dimension that client can request. Requests with
``page_size`` greater of ``maxPageSize`` returns a 400 HTTP error.

If you want modify those defaults you can simply override that property
in ``ApiController``.

Paginate objects in custom endpoints
------------------------------------

When a request has ``page`` or ``page_size`` as GET parameters those are
validated and ``$paginationOptions`` is updated to contain the requested
page options. A ``dim`` key equal to ``pageSize`` is added to be ready
to use in some methods as ``FrontendController::loadSectionObjects()``.

In this way in a 'custom' API endpoint you can simply do

.. code-block:: php

    protected function custom($id) {
        $result = $this->loadSectionObjects($id, $this->paginationOptions);
        // format and set pagination
        $this->setPaging($this->ApiFormatter->formatPaging($result['toolbar']));

        // do other stuff
    }

and you are sure that pagination will work properly without doing
anything else.
