**********
Quickstart
**********

In this quickstart guide, we’re going to walk through the steps to import some sample data, run the LSC synchronisation, and look at what it’s done.

The sample CSV data, a Java embedded database ((:doc:`HSQLDB <hsqldb>`)), and directory (`OpenDJ <https://github.com/OpenIdentityPlatform/OpenDJ>`__) are all included in the LSC download. This means you can see it working without having to set up your own dataset or database.

Prerequisites
=============

* A `Java Virtual Machine (JVM) <https://lsc.readthedocs.io/en/latest/requirements.html>`_  installed on your system with correct version (see :doc:`requirements <requirements>`).

Download the latest version of LSC
==================================

On Linux/Mac
------------
1. Download the latest Tarball LSC core archive from the `download area <https://lsc-project.org/download.html>`_
2. Decompress the archive using the following commands:

.. code-block:: console

    unzip lsc-core-*-dist.zip
    rm lsc-core-*-dist.zip
    cd lsc-*

3. The LSC installation is now complete.

On Windows (with `7-Zip <https://www.7-zip.org>`_)
--------------------------------------------------
1. Download the latest Tarball LSC core archive from the `download area <https://lsc-project.org/download.html>`_
2. In File Explorer, select the download and click ‘Extract all’
3. Select where you’d like to install LSC and click ‘Extract’
4. Delete the original .zip file
5. The LSC installation is now complete.

Sample data
===========

For this quickstart tutorial, we’re going to synchronise a sample list of users into an LDAP directory. This list is included in the package as a csv file under ``sample/hsqldb/sample.csv``.

This is what’s included in that csv file:

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

Load the CSV file into a database
---------------------------------

Before LSC can read the data from the CSV file, you need to load it into a database first. This guide uses an embedded database, :doc:`HSQLDB <hsqldb>`, so no installation or configuration is required.

To set up a new database and import the CSV file, open terminal/CMD in the newly extracted lsc folder then run these commands:

.. code-block:: console

    cd lsc-*
    cd sample/hsqldb
    bin/lsc-sample --import sample.csv
    
If all goes well, this should display a few lines of messages:

.. code-block:: console

    Table csvdata created
    8 lines added to table csvdata

.. note::

   You can change the data in the CSV file and repeat this step as many times as you want. The database will be reinitialized each time.

Check the database contents
---------------------------

To see what’s in the database, you can run this command:

.. code-block:: console

    bin/lsc-sample --show

This should display a simple table containing the information from sample.csv.

Create an empty LDAP directory
==============================

To make this tutorial easier, an open-source directory server (`OpenDJ <https://github.com/OpenIdentityPlatform/OpenDJ>`_) is bundled with the sample directory of LSC.

To launch the empty LDAP directory, open a separate shell and run this command:

.. code-block:: console

    bin/lsc-sample --start-ldap-server

.. note::

   You need to leave this shell running for your LDAP directory to work
 
If all goes well, this should display a few lines of messages:

.. code-block:: console

    Starting LDAP server on ldap://localhost:33389/ ...
    ... LDIF sample content loaded successfully

.. note::

   This may take a minute or two to launch, please be patient

Check the content of the directory
----------------------------------

Using whatever LDAP browser you like, check out the content of the directory. Use the following parameters to connect to the directory.

**(We recommend `Apache Directory Studio <http://directory.apache.org/studio/>`_ as a nice multiplatform, graphical LDAP browse)**

- **Hostname:** ``localhost``
- **Port:** ``33389``
- **Base DN:** ``dc=lsc-project,dc=org``
- **Bind DN:** ``cn=Directory Manager``
- **Password:** ``secret``

You should see only two entries, the basic structure:

* ``dc=lsc-project,dc=org``

    * ``ou=Sample``

Run the synchronization
=======================

Now we have a data source to use and an empty LDAP directory, it’s time to fire up LSC.

.. warning::

   From now on, use a different terminal from the one the LDAP directory is running in.

A wrapper script makes it easy to launch, just run this command from the main directory:

.. code-block:: console

    bin/lsc-sample --run

*Or, if you had the exception earlier, run this:*

.. code-block:: console

    JAVA_HOME=`/usr/libexec/java_home -v 1.8` bin/lsc-sample --run

This will display:

.. code-block:: console

    Running /usr/local/lsc-2.0/bin/lsc \
    --config /usr/local/lsc-2.0/sample/etc --synchronize all --clean all

This runs the ``bin/lsc command``, telling it to use the sample/etc directory for configuration, then synchronize and clean all defined tasks.

Detailed information about the added entries will then be printed, before these three lines:

.. code-block:: console

    All entries: 8, to modify entries: 8, successfully modified entries: 8, errors: 0
    Starting clean for MySyncTask
    All entries: 8, to modify entries: 0, successfully modified entries: 0, errors: 0


Admire the results
==================

Now this synchronization has run, your LDAP directory should contain one entry for each line from our CSV file:

- ``dc=lsc-project,dc=org (1)``
    - ``ou=Sample (8)``
        - ``mail=clem.oudot@gmail.com``
        - ``mail=dcoutadeur@linagora.com``
        - ``mail=epereira@linagora.com``
        - ``mail=jonathan@philipoux.net``
        - ``mail=remy@schermesser.com``
        - ``mail=rouazana@linagora.com``
        - ``mail=sebastien.bahloul@gmail.com``
        - ``mail=thomas@aepik.net``

Running it again changes nothing
================================

If you launch the synchronization again, you’ll see that nothing more is changed in the directory. This is because LSC compares all the data from our source, and works out that everything is up to date:

.. code-block:: console

    All entries: 8, to modify entries: 0, successfully modified entries: 0, errors: 0

Play around
===========

Now you have the basic synchronization working, have a play around with the data and settings, to get a feel for what LSC can do.
Here are some examples:


**Edit some names in sample.csv and reload the file:**

.. code-block:: console

    bin/lsc-sample --import sample.csv

Then re-run the synchronization, to see how simple modifications are synchronized:

.. code-block:: console

    bin/lsc-sample --run

**Remove a row in sample.csv and reload the file:**

.. code-block:: console

    bin/lsc-sample --import sample.csv

Then re-run the synchronization, to see how entries are deleted:

.. code-block:: console

    bin/lsc-sample --run

**Read the main configuration file in etc/lsc.xml and add the following lines to the file:**


.. code-block:: html

    <dataset>
        <name>sn</name>
        <forceValues>
            <string>js:srcBean.getDatasetFirstValueById("sn").toUpperCase()</string>
        </forceValues>
    </dataset>

Then re-run the synchronization, and you’ll see all surnames are now in upper-case:

.. code-block:: console

    bin/lsc-sample --run

Stopping the LDAP server
========================

When you’re done with the sample, you can stop the LDAP server by pressing ``“Control-C”`` in its shell. Then, simply remove the whole directory (if you want):

.. code-block:: console

    rm -r sample

What's next?
============

Once you've had a play with this sample data, you probably want to move on to your own synchronization.

The main configuration file is in ``etc/lsc.xml``. It is the same format as the one from the sample, so you'll be able to use it quickly. A sample file is provided in ``etc/lsc.xml-sample``, just rename it to get started.

Read through the :doc:`documentation <index>` on this web site. If you need help or have a question, `get in touch <https://lsc-project.org/contact.html>`__.

Last but not least, we really hope you enjoy using LSC, and it solves problems for you. We'd love to hear back from you.

