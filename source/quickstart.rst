**********
Quickstart
**********

A sample configuration with some data is included in the LSC download. This page will show you how to run it, to get started with LSC.

This demo uses CSV data, a Java embedded database (:doc:`HSQLDB <hsqldb>`) and directory (`OpenDJ <https://github.com/OpenIdentityPlatform/OpenDJ>`__), so it just works out of the box :)

Follow the steps below to get a feel of what LSC can do!

Download the latest version of LSC
==================================

.. note::

    This tutorial assumes you are using LSC source tarball.

- Make sure you have a JVM installed (see the :doc:`Requirements <requirements>`)
- Download the latest LSC core archive from the `download area <https://lsc-project.org/download.html>`__
- Decompress the archive and enter the directory:

.. code-block:: console

    $ unzip lsc-core-*-dist.zip
    $ rm lsc-core-*-dist.zip
    $ cd lsc-*

Our data source: a CSV file of users
====================================

The ``sample/hsqldb/sample.csv`` file is a list of users, with their information stored in CSV format. Have a look at this file to see the contents.

+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| ID   | UID           | ENDOFVALIDITY | SN          | CN                              | GIVENNAME        | MAIL                        | O         | ADDRESS                                   | TELEPHONENUMBER   |
+======+===============+===============+=============+=================================+==================+=============================+===========+===========================================+===================+
| 1    | j.clarke      | 31/12/2015    | Clarke      | Clarke, Jonathan                | Jonathan         | jonathan@philipoux.net      | Normation |                                           |                   |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 2    | r.schermesser | 31/12/2015    | Schermesser | Schermesser, Remy-Christophe    | Remy-Christophe  | remy@schermesser.com        | Octo      |                                           |                   |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 3    | t.chemineau   | 31/12/2015    | Chemineau   | Chemineau, Thomas               | Thomas           | thomas@aepik.net            | AFNOR     |                                           |                   |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 4    | s.bahloul     | 31/12/2015    | Bahloul     | Bahloul, Sebastien              | Sebastien        | sebastien.bahloul@gmail.com | Dictao    | 156 av. de Malakof, 75116 PARIS, France   |                   |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 5    | c.oudot       | 31/12/2015    | Oudot       | Oudot, Clement                  | Clement          | clem.oudot@gmail.com        | Linagora  |                                           | 33(0)810251251    |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 6    | r.ouazana     | 31/12/2015    | Ouazana     | Ouazana, Raphael                | Raphael          | rouazana@linagora.com       | Linagora  |                                           | 33(0)810251251    |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 7    | d.coutadeur   | 31/12/2015    | Coutadeur   | Coutadeur, David                | David            | dcoutadeur@linagora.com     | Linagora  |                                           | 33(0)810251251    |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+
| 8    | e.pereira     | 31/12/2015    | Pereira     | Pereira, Esteban                | Esteban          | epereira@linagora.com       | Linagora  |                                           | 33(0)810251251    |
+------+---------------+---------------+-------------+---------------------------------+------------------+-----------------------------+-----------+-------------------------------------------+-------------------+

The aim of this demonstration is to synchronize this file into a LDAP directory.

Load the CSV file into a database
---------------------------------

For LSC to read data from the CSV file, we must load it into a database. This demonstration uses an embedded database, :doc:`HSQLDB <hsqldb>`, so no installation or configuration is necessary.

To set up a new database and import the CSV file, run this command:

.. code-block:: console

    $ cd sample/hsqldb
    $ bin/lsc-sample --import sample.csv


If all goes well, this should display a few lines of messages:

.. code-block:: console

    Table csvdata created
    8 lines added to table csvdata

You can change the data in the CSV file and repeat this step as many times as you want. The database will be reinitialized each time.

Check the contents of the database
----------------------------------

Now, let's see what the database contains. Run the following command:

.. code-block:: console

    $ bin/lsc-sample --show

You should see a simple table containing the information from our CSV file, ``sample.csv``.

Our data destination: an empty LDAP directory
=============================================

An open source directory server, `OpenDJ <https://github.com/OpenIdentityPlatform/OpenDJ>`__, is bundled in the sample directory of LSC. To launch it, you need a separate shell, so you can leave it running:

.. code-block:: console

    $ bin/lsc-sample --start-ldap-server

If all goes well, this should display a few lines of messages:

.. code-block::

    Starting LDAP server on ldap://localhost:33389/ ...
    10766 - INFO  - LDIF sample content loaded successfully

.. important::

    This may take a minute or two to launch. Please be patient!

Check the content of the directory
----------------------------------

Using whatever LDAP browser you like, check out the content of the directory. The parameters to connect are:

* URL: ``ldap://localhost:33389/``
* Base DN: ``dc=lsc-project,dc=org``
* Bind DN: ``cn=Directory Manager``
* Password: ``secret``

You should see only two entries, the basic structure:

* dc=lsc-project,dc=org

    * ou=Sample

.. tip::

    We recommend `Apache Directory Studio <http://directory.apache.org/studio/>`__ as a nice multiplatform, graphical LDAP browser.

Run the synchronization
=======================

Now we have a data source and an empty LDAP directory just waiting for us to write in it, let's fire up LSC.

.. important::

    From now on, use a different terminal from the one the LDAP directory is running in.

A wrapper script makes it easy to launch, just run this command from the main directory:

.. code-block:: console

    $ bin/lsc-sample --run

This will display:

.. code-block::

    Running /usr/local/lsc-2.0/bin/lsc \
    --config /usr/local/lsc-2.0/sample/etc --synchronize all --clean all


This actually runs the ``bin/lsc`` command, telling it to use the ``sample/etc`` directory for **configuration**, and **synchronize** **all** defined tasks, and **clean** **all** defined task.

Detailed information about added entries will then be printed, before a summary line:

.. code-block::

    All entries: 8, to modify entries: 8, modified entries: 8, errors: 0

You should also see the same line for the clean phase:

.. code-block::

    All entries: 8, to modify entries: 0, successfully modified entries: 0, errors: 0

Admire the results
==================

Now this synchronization has run, your LDAP directory should contain one entry for each line from our CSV file:

* dc=lsc-project,dc=org

    * ou=Sample

        * mail=clem.oudot@gmail.com
        * mail=dcoutadeur@linagora.com
        * mail=epereira@linagora.com
        * mail=jonathan@philipoux.net
        * mail=remy@schermesser.com
        * mail=rouazana@linagora.com
        * mail=sebastien.bahloul@gmail.com
        * mail=thomas@aepik.net

Run it again
============

If you launch the synchronization again, you'll see that nothing more is changed in the directory. This is because LSC compares all the data from our source, and works out that everything is up to date:

.. code-block::

    All entries: 8, to modify entries: 0, modified entries: 0, errors: 0

Play around
===========

Now you have the basic synchronization working, have a play around with the data and settings, to get a feel for what LSC can do.

Here are some examples:

- Edit some names in ``sample.csv`` and reload the file:

.. code-block:: console

    $ bin/lsc-sample --import sample.csv

Then re-run the synchronization, to see how simple modifications are synchronized:

.. code-block:: console

    $ bin/lsc-sample --run

- Remove a row in ``sample.csv`` and reload the file:

.. code-block:: console

    $ bin/lsc-sample --import sample.csv

Then re-run the synchronization, to see how entries are deleted:

.. code-block:: console

    $ bin/lsc-sample --run

- Read the main configuration file in ``etc/lsc.xml`` and add the following lines to the file:

.. code-block:: XML

    <dataset>
        <name>sn</name>
        <forceValues>
            <string>js:srcBean.getDatasetFirstValueById("sn").toUpperCase()</string>
        </forceValues>
    </dataset>

Then re-run the synchronization, and you'll see all surnames are now in upper-case:

.. code-block:: console

    $ bin/lsc-sample --run

When you're done with the sample, you can stop the LDAP server by pressing "Control-C" in its shell. Then, simply remove the whole directory (if you want):

.. code-block:: console

    $ rm -r sample

What's next?
============

Once you've had a play with this sample data, you probably want to move on to your own synchronization.

The main configuration file is in ``etc/lsc.xml``. It is the same format as the one from the sample, so you'll be able to use it quickly. A sample file is provided in ``etc/lsc.xml-sample``, just rename it to get started.

Read through the :doc:`documentation <index>` on this web site. If you need help or have a question, `get in touch <https://lsc-project.org/contact.html>`__ by mailing lists or IRC.

Last but not least, we really hope you enjoy using LSC, and it solves problems for you. We'd love to hear back from you.

