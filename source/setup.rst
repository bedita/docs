*****
Setup
*****

Prerequisites
=============

 * PHP 7.x (recommended) or PHP >= 5.6, with extensions *mbstring*, *intl*
 * MySQL 5.7 (recommended) or MySQL 5.6, Postgres 9.5/9.6 or SQLite 3
 * `Composer <https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx>`_

A web server like Apache or Nginx also recommended in general, but not strictly necessary
on a development enviroment.

OS: there are no virtually constraints or limitations, but a recent Linux distribution
is recommended for a server setup and Linux is the only officially supported server platform.

For a development/test setup you may choose any modern desktop/server OS.
BEdita dev team uses mainly Linux and MacOSX so some strange things may happen using other systems :)

But let us know if you have any OS specific troubles, we will be happy to help.


Install
=======


There are three basic steps to install BEdita 4

**1. Create project via composer**

.. code-block:: bash

  $ composer create-project -s dev bedita/bedita

If you are using a **.zip** or **.tar.gz** release file you just need to unpack it and then run ``composer install``.

**2. Create an empty database**

Create an empty MySQL or Posgtres database and keep track of main connection parameters
like ``host``, ``username``, ``password`` and ``database``.
Do nothing for SQLite since a new local file will be created.

**3. Run shell script to initialize the database and create first admin user**

Open a shell prompt on root installation folder and write

.. code-block:: bash

  $ bin/cake bedita setup

An interactive shell script will guide you through missing installation steps:
 * database setup & schema check
 * filesystem check
 * admin user creation

You may see the first admin user created like a root user on Linux or MySQL: it has **administrator**
:term:`role` privileges and cannot be removed.

To setup a database connection you may also edit the main configuration file, see `Manual Setup`_.

Web Server
==========

On a development setup it's possible to use PHP builtin webserver, have a look
at `CakePHP Development Server <http://book.cakephp.org/3.0/en/installation.html#development-server>`_
Using this simple command

.. code-block:: bash

  $ bin/cake server

This will serve the backend application at http://localhost:8765/.

This setup should be used **ONLY** in development and **NEVER** as a production setup.

For test and production setups is always advisable to use a **real** web server like Apache2 or Nginx.


Apache2
-------

Below some instructions for **Apache 2.4**, but also other versions of Apache, like 2.2, will work.

Please make sure that **mod_rewrite** is enabled. On Ubuntu 14.04 or 16.04 or on Debian 8 you may verify it like this

.. code-block:: bash

  $ more /etc/apache2/mods-enabled/rewrite.load
  LoadModule rewrite_module /usr/lib/apache2/modules/mod_rewrite.so

On other systems with different Apache configurations this check should be similar.

A simple minimal working virtualhost configuration can look like this:

.. code-block:: apache

    <VirtualHost *:80>
        ServerName api.example.com

        DocumentRoot /path/to/bedita/webroot
        <Directory /path/to/bedita/webroot>
            Options FollowSymLinks
            AllowOverride All
            Require all granted
        </Directory>
    </VirtualHost>

Where:
 * ``/path/to/bedita`` refers simply to the **Backend** path on filesystem
 * ``DocumentRoot`` should point to the ``webroot`` folder
 * ``AllowOverride All`` is needed to enable ``.htaccess`` files
 * ``Require all granted`` allows access from anywhere, you may decide to set some restrictions based on hosts/IP

To enable `CORS <https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS>`_ on virtualhost configuration
you may add these lines, provided that **mod_headers** is enabled

.. code-block:: apache

    Header set Access-Control-Allow-Origin "*"
    Header set Access-Control-Allow-Headers "content-type, origin, x-requested-with, authorization"
    Header set Access-Control-Allow-Methods "PUT, GET, POST, PATCH, DELETE, OPTIONS"
    Header set Access-Control-Expose-Headers "Location"

In this example:
 * all origins and HTTP methods are allowed, you may want to add restrictions
 * only headers used by BEdita4 are allowed
 * "Location" header is exposed in response, this is useful to get URL of a newly created resource

Alternatively you can setup *CORS* configuration directly in BEdita, see :ref:`configuration-cors`

Nginx
-----

[TBD]


Manual Setup
============

To setup database connection manually or review the current connection
you may edit the main default configuration file located in ``config/app.php`` where datasources are defined.

Look for ``Datasources`` array definition then modify ``host``, ``username``,
``password`` and ``database`` fields.

.. code-block:: php

    'Datasources' => [
        'default' => [
            'className' => 'Cake\Database\Connection',
            'driver' => 'Cake\Database\Driver\Mysql',
            'host' => 'localhost',
            //'port' => 'non_standard_port_number',
            'username' => '......',
            'password' => '......',
            'database' => '......',
            .....
        ],
    ]

Other noteworthy fields:
 * ``port`` - populate only in case of non standard ports
 * ``driver`` - change to ``'Cake\Database\Driver\Postgres'`` or ``'Cake\Database\Driver\Sqlite'`` accordingly
 * for SQlite you need to set only an absolute local file path in ``database``
