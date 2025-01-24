********
Overview
********

BEdita is a tool to handle the data of your mobile, IoT, web and desktop applications.

It's a full fledged content management solution with:
 * an HTTP REST driven server application - the **Backend** - with a complete API to model, create, modify and retrieve data
 * a default progressive web application - the **Manager** - to control & manage your data

..  * **Tools** to create applications upon BEdita REST API

BEdita is built with `CakePHP 3 <http://cakephp.org>`_ and uses relational DBMS like `MySQL <http://www.mysql.com>`_,
`Postgres <https://www.postgresql.org>`_ or `SQLite <http://sqlite.com>`_
in conjunction with (optional) NoSQL systems like `Redis <http://redis.io/>`_, `Elastic Search <https://www.elastic.co/>`_ and `InfluxDb <https://www.influxdata.com/time-series-platform/influxdb/>`_ to boost
performance and scale up to Big Data scenarios.

`JSON API <http://jsonapi.org>`_ is the primary, although not unique, exchange data format.


Backend
=======

The **Backend** is the core of BEdita: an HTTP REST service that exposes a set of API endpoints.

It's not a standalone application service or daemon like a DBMS, but it's an application deployed in a Web server like Apache or Nginx
and accessible via standard HTTP ports.

So at first it may look like a *standard* Web application, but it's not.

It doesn't offer an HTML interface, it doesn't respond in HTML [#a]_ but only in JSON [#b]_: the only way to communicate
with this service is through its :term:`JSON API` REST endpoints. See :doc:`endpoints/index` for an overview.


To install a backend instance see :doc:`setup`



Manager
=======

The **Manager** is an hybrid cakephp+vue web application to manage all of your data using the backend REST API.

You may see this application as the primary GUI of BEdita or as a *control panel* or *admin console*.

To operate with this application you need to login first.

The first user created during setup has the permanent :term:`role` of *administrator* and using its credentials you may:

* create new users and assign roles to them
* create new roles
* create and modify objects - contents or tree structures that may be used in your applications
* create new object types from your application domain or extend existing types
* create relations between objects
* give access permissions to endpoints and objects to some roles
* set configuration settings and view system information and relevant events
* model your objects
* setup configurations and much more

Through its dynamic permission system keep in mind that users having different roles may perform different actions:

* some users may not be able to login in the **Manager** - e.g. standard users of your applications
* some users may not have access to some endpoints - like modeling new types, or view some object types
* some users may have a read-only access to some endpoints - they may only see certain types but not create or modify anything

..
    [TEMPORARY COMMENTED OUT]
    Tools
    =====

    The goal of BEdita is to let you build better applications and create your application faster.
    Therefore some tools are provided to help you:

    * simple application examples
    * libraries in javascript and other languages/platforms

    These tools may help you, but are not strictly necessary: BEdita API is built on open
    and well documented standards and protocols like **HTTP**, **JWT** or **JSON API**.

    You should be able to use the API using *curl* or the most sophisticated Java framework also without these tools.
..

.. rubric:: Notes

.. [#a] it's possible however, only for testing/debugging purposes, to activate HTML responses
        to use the **Backend** in a browser, see :ref:`configuration-accept`
.. [#b] other formats like YML (or XML) could be added in future releases.
