*****
Setup
*****

Prerequisites
=============

 * PHP 7 (recommended) or PHP >= 5.5.9, with extensions *mbstring*, *intl*
 * MySQL >= 5.5 (Posgtres and SQLite support in future releases)
 * [Composer](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx)


A web server like Apache or Nginx also recommended in general, but not strictly necessary
on a development enviroment.

OS: there are no virtually constraints or limitations, but a recent Linux distribution
is recommended for a server setup and Linux is the only officially supported server platform.

For a development/test setup you may choose any modern desktop/server OS.
BEdita dev team uses mainly Linux and MacOSX so some strange things may happen using other systems :)

But let us know if you have any OS specific troubles, we will be happy to help.


Install
=======


There are four basic steps to install BEdita 4

**1. Clone git repository**

.. code-block:: bash

  $ git clone -b 4-develop https://github.com/bedita/bedita.git

**2. Run composer install**

.. code-block:: bash

  $ cd bedita
  $ composer install

**3. Configure your database connection**

Create an empty MySQL (or Posgtres or SQLite, in the future) database and edit
CakePHP default configuration file located in ``config/app.php`` where datasources are defined.

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

**4. Run shell script to initialize the database and create first admin user**

Open a shell prompt on root installation folder and write

.. code-block:: bash

  $ bin/cake bedita setup

An interactive shell script will guide you through missing installation steps:
 * database setup & schema check
 * filesystem check
 * admin user creation

You may see the first admin user created like a root user on Linux or MySQL: it has **administrator**
:term:`role` privileges and cannot be removed.


Web Server
==========


Apache2
-------


Nginx
-----

Advanced setup
==============
