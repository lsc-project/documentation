******
HSQLDB
******

`HSQLDB (Hyper SQL Database) <https://hsqldb.org/>`__ is a relational database management system written in Java. It offers a fast, small database engine which brings both in-memory and disk-based tables.

LSC provides `HSLQDB <https://hsqldb.org/doc/>`__ library and a utility script that allows to use this database to store temporary synchronization data, for example to use a CSV file as a source.

Script location
===============

LSC provides a wrapper to manage the packaged HSQLDB database, it is located in the LSC bin directory and is named ``hsqldb``.

You can execute it to see the possible options:

.. code-block:: console

    $ bin/hsqldb 

     bin/hsqldb [<option>]

     One option from available options is:
       --import <file> [<table>] [<separator>]    Import data from CSV file
       --drop [<table>]   Drop data
       --show [<table>]   Show data from database
       --start   Start the server
       --stop    Stop the server
       --status  Display server status
       --help    Print informations

Start/stop HSQLDB
=================

Start HSQLB will the following command:

.. code-block:: console

    $ bin/hsqldb --start

You can then check the status:

.. code-block:: console

    $ bin/hsqldb --status
    HSQLDB Server is running (PID 7020)

To stop it:

.. code-block:: console

    $ bin/hsqldb --stop

.. important::

    If you stop HSQLDB, all registered data will be lost.

Import CSV data
===============

By default, your HSQLDB database is empty. You can load the data with this command:

.. code-block:: console

    $ bin/hsqldb --import tutorial.csv 
    1 lines imported into table csvdata

.. tip::

    By default, data are loaded in table ``csvdata`` but you can change the table name, for example:

.. code-block:: console

    $ bin/hsqldb --import tutorial.csv tutorial
    1 lines imported into table tutorial

You can then display imported data:

.. code-block:: console

    $ bin/hsqldb --show
    UID   SN   GIVENNAME  CN        MAIL
    ----  ---  ---------  --------  ----------------
    jdoe  Doe  John       John Doe  jdoe@example.com

.. tip::

    You can specify the table name after the ``--show`` option.

Remove data
===========

You can remove data by dropping a table:

.. code-block:: console

    $ bin/hsqldb --drop

.. tip::

    You can specify the table name after the ``--drop`` option.

Run SQL queries
===============

You can run SQL queries on HSQLDB database :

.. code-block:: console

    # Generic (see lsc-sample in doc)
    $ "${JAVA_COMMAND}" -jar "$HSQLDB_LIB" --rcFile "$HSQLDB_RC" --sql "YOUR_SQL_QUERY" lscdb
    
    # Debian
    $ java -jar /usr/lib/lsc/hsqldb-VERSION.jar --rcFile /var/lsc/hsqldb/hsqldb.rc --sql 'YOUR_SQL_QUERY' lscdb

.. important::

    Don't forget ``;`` at the end of SQL query

Example of HSQLDB connector
===========================

If you want a complete example of HSQLDB source connector with LSC, give a look at :doc:`Synchronize from CSV to LDAP <csvtoldap>` howto.



