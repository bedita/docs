**************
Shell commands
**************

A number of administration tasks can be performed via shell.

.. _shell:bedita:

``bedita``
==========

The ``bedita`` shell is the first place you'll ever want to search in
when you're looking for a tool to perform an administrative task.

.. _shell:bedita:setup:

``setup``
---------

The ``setup`` subcommand will lead you through the several steps required to
get a new BEdita instance to work.

You'll be asked for database connection credentials, as well as user
credentials for the first administrator user to be added to the system.

Additional steps, like checking for correct permissions on some folders,
creating database tables, or checking if current database schema is
up-to-date with the latest changes, are performed.

**It is safe to run this command again once BEdita has been initialized.**

.. program:: bedita setup

.. option:: -y, --yes

   Run in non-interactive mode.

.. option:: --config-file <path>

   Path to configuration file (default: :file:`config/app.php`).

.. _shell:data_seed:

``data_seed``
=============

The ``data_seed`` shell can be used to populate the database with fake data
in experimental or testing scenarios.

.. _shell:data_seed:insert:

``insert``
----------

The ``insert`` subcommand will populate the database with random fake data.

.. program:: data_seed insert

.. option:: -t <table>, --table <table>

   Database table name to insert data in (default: ``users``).

.. option:: -n <number>, --number <number>

   Number of rows to be added in the table (default: 1).

.. option:: -f <fields>, --fields <fields>

   Default values for fields. Useful when you want to create large amount of
   objects that share some common trait. It must be provided in the form of a
   comma-separated list of ``key=value`` pairs.

.. _shell:db_admin:

``db_admin``
============

The ``db_admin`` shell is specialized in database administration tasks
for developers. As a user of BEdita, you'll hardly need any of the commands
that this shell provides.

.. _shell:db_admin:init:

``init``
--------

This subcommand will initialize the database. The database connection **must**
be already configured in order for this command to work. If you're looking for
a command-line wizard to guide you through all the steps required to install
BEdita, please head to the :ref:`setup command <shell:bedita:setup>`.

If any table is present in the database, you'll be asked if you want your
database to be weeped, or if you wish to abort the operation. When the schema
has been created, you'll also be asked if you want additional set of data to be
loaded into the database. A minimum set of data is loaded regardless of your
choice --- this is required in order for BEdita to work.

This command can be run in unattended (non-interactive) mode by appending
command-line flags ``--[no-]force`` and ``--[no-]seed``.

.. program:: db_admin init

.. option:: -f, --force

   Force removal of all tables in case target database is not empty.

.. option:: --no-force

   Abort if target database is not empty.

.. option:: -s, --seed

   Seed database with additional set of data.

.. option:: --no-seed

   Don't load any additional set of data. A minimum set of data required for
   BEdita to work is loaded anyway.

.. option:: -c <connection>, --connection <connection>

   Database connection to be used (default: ``default``).

.. _shell:db_admin:check_schema:

``check_schema``
----------------

This subcommand will perform checks on the current schema. This command is
mostly useful when developing features that require making changes to the
schema of BEdita's database.

You'll be notified of:

- migration history not in sync (schema not migrated to the latest
  available version)
- new changes (added or removed tables; changes to columns,
  indexes or constraints)
- naming that offends SQL conventions

This command exits with a non-zero exit code whenever current schema is not
completely up-to-date and it follows SQL conventions, making it possible to
employ this command in other automated tasks.

.. program:: db_admin check_schema

.. option:: -c <connection>, --connection <connection>

   Database connection to be used (default: ``default``).

.. _shell:db_admin:save_schema:

``save_schema``
---------------

This subcommand is deprecated. Run :command:`bin/cake migrations dump` instead.

.. program:: db_admin save_schema

.. option:: -c <connection>, --connection <connection>

   Database connection to be used (default: ``default``).

.. _shell:spec:

``spec``
========

The ``spec`` shell can be used to automatically generate piece of documentation
for available API endpoints.

.. _shell:spec:generate:

``generate``
------------

The ``generate`` subcommand will generate Swagger documentation in YAML format
for currently available API endpoints.

.. program:: spec generate

.. option:: -o <output>, --output <output>

   Specify an output file (default: :file:`plugins/BEdita/API/spec/be4.yaml`).
