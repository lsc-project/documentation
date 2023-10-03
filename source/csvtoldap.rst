**************************************
Synchronize from CSV to LDAP directory
**************************************

This tutorial explains how you can synchronize a CSV file to an LDAP directory.

The idea is to use a database source service based on a HSQLDB instance (database in files or in memory).

This HSQLDB instance will be first populated with data contained in the CSV file.

.. tip::

    To run a sample CSV to LDAP connector out of the box, you can follow the :doc:`quickstart tutorial <quickstart>`.

.. note::

    Another way to synchronize from CSV to LDAP is to use `Executable plugin <https://github.com/lsc-project/lsc-executable-plugin>`__ and `Perl wrappers for CSV <https://github.com/lsc-project/lsc-executable-plugin/blob/master/doc/perl-csv.md>`__.

Step 1: Populate the HSQLDB database from CSV
=============================================

CSV file
--------

First, get a CSV file, or create one from this tutorial. This file will be called ``tutorial.csv`` and contains the following sample data:

.. code-block::

    uid;sn;givenName;cn;mail
    jdoe;Doe;John;John Doe;jdoe@example.com

HSQLDB script
-------------

LSC provides a wrapper to manage the packaged HSQLDB database, see :doc:`HSQLDB <hsqldb>` to get more details.

Start HSQLDB
------------

Start HSQLB with the following command:

.. code-block:: console

    $ bin/hsqldb --start

You can then check the status:

.. code-block:: console

    $ bin/hsqldb --status
    HSQLDB Server is running (PID 7020)

Import CSV data
---------------

Load the data with this command:

.. code-block:: console

    $ bin/hsqldb --import tutorial.csv 
    1 lines imported into table csvdata

Check imported data:

.. code-block:: console

    $ bin/hsqldb --show
    UID   SN   GIVENNAME  CN        MAIL
    ----  ---  ---------  --------  ----------------
    jdoe  Doe  John       John Doe  jdoe@example.com

Step 2: Configure LSC
=====================

.. note::

    We just provide here the specific configuration items for this tutorial. For a complete overview of LSC configuration, please read :ref:`configuration <index-configuration>`.

HSQLDB source connector
-----------------------

First of all, configure your :doc:`database connection <connection_database>`:

.. code-block:: XML

    <databaseConnection>
        <name>src-jdbc</name>
        <url>jdbc:hsqldb:hsql://localhost/lscdb</url>
        <username>sa</username>
        <password></password>
        <driver>org.hsqldb.jdbcDriver</driver>
    </databaseConnection>

Then configure your service by referencing the previous connection ("reference" attribute is pointing to the connection node):

.. code-block:: XML

    <databaseSourceService>
        <name>user-src</name>
        <connection reference="src-jdbc"/>
        <requestNameForList>getInetOrgPersonList</requestNameForList>
        <requestNameForObject>getInetOrgPerson</requestNameForObject>
        <requestNameForClean>getInetOrgPersonClean</requestNameForClean>
    </databaseSourceService>

We now need to use IBatis to get data from HSQLDB. First, create or update the SQL map configuration:

.. code-block:: console

    $ vi etc/sql-map-config.xml

.. code-block:: XML

    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE sqlMapConfig
        PUBLIC "-//ibatis.apache.org//DTD SQL Map Config 2.0//EN"
        "http://ibatis.apache.org/dtd/sql-map-config-2.dtd">
    <sqlMapConfig>
        <transactionManager type="JDBC">
            <dataSource type="SIMPLE">
                <property value="${driver}" name="JDBC.Driver" />
                <property value="${url}" name="JDBC.ConnectionURL" />
                <property value="${username}" name="JDBC.Username"/>
                <property value="${password}" name="JDBC.Password"/>
                <property value="15" name="Pool.MaximumActiveConnections"/>
                <property value="15" name="Pool.MaximumIdleConnections"/>
                <property value="1000" name="Pool.MaximumWait"/>
            </dataSource>
        </transactionManager>
        <sqlMap url="file://${lsc.config}/sql-map-config.d/InetOrgPerson.xml"/>
    </sqlMapConfig>

Then declare SQL queries:

.. code-block:: console

    $ vi etc/sql-map-config.d/InetOrgPerson.xml

.. code-block:: XML

    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
    <!DOCTYPE sqlMap PUBLIC "-//iBATIS.com//DTD SQL Map 2.0//EN" "http://www.ibatis.com/dtd/sql-map-2.dtd">

    <sqlMap namespace="InetOrgPerson">

            <select id="getInetOrgPerson" resultClass="java.util.HashMap" parameterClass="java.util.Map">
                    SELECT
                    pers."UID" AS uid,
                    pers."SN" AS sn,
                    pers."GIVENNAME" AS givenname,
                    pers."CN" AS cn,
                    pers."MAIL" AS mail
                    FROM csvdata pers
                    WHERE pers."UID" LIKE #uid#
            </select>

            <select id="getInetOrgPersonList" resultClass="java.util.HashMap">
                    SELECT pers."UID" AS uid
                    FROM csvdata pers
            </select>

            <select id="getInetOrgPersonClean" resultClass="java.util.HashMap" parameterClass="java.util.Map">
                    SELECT
                    pers."UID" AS UID
                    FROM csvdata pers
                    WHERE pers."UID" LIKE #uid#
            </select>

    </sqlMap>

LDAP destination connector
==========================

.. tip::

    We suppose you have a running LDAP server on localhost. If not, you can use the sample LDAP directory from the :doc:`quickstart <quickstart>`.

First of all, configure your :doc:`ldap connection <connection_ldap>`:

.. code-block:: XML

    <ldapConnection>
        <name>dst-ldap</name>
        <url>ldap://localhost:33389/dc=lsc-project,dc=org</url>
        <username>cn=Directory Manager</username>
        <password>secret</password>
        <authentication>SIMPLE</authentication>
        <referral>IGNORE</referral>
        <derefAliases>NEVER</derefAliases>
        <version>VERSION_3</version>
        <pageSize>-1</pageSize>
        <factory>com.sun.jndi.ldap.LdapCtxFactory</factory>
        <tlsActivated>false</tlsActivated>
        <saslMutualAuthentication>false</saslMutualAuthentication>
    </ldapConnection>

Then configure LDAP destination service:

.. code-block:: XML

    <ldapDestinationService>
        <name>user-dst</name>
        <connection reference="dst-ldap"/>
        <baseDn>ou=Sample,dc=lsc-project,dc=org</baseDn>
        <pivotAttributes>
            <string>uid</string>
        </pivotAttributes>
        <fetchedAttributes>
            <string>cn</string>
            <string>sn</string>
            <string>gn</string>
            <string>uid</string>
            <string>userPassword</string>
            <string>objectClass</string>
            <string>mail</string>
        </fetchedAttributes>
        <getAllFilter>(objectClass=inetOrgPerson)</getAllFilter>
        <getOneFilter>(&amp;(objectClass=inetOrgPerson)(uid={uid}))</getOneFilter>
    </ldapDestinationService>

Set synchronization rules
=========================

As usual, define also how the synchronized objects are going to be identified and how you want to force or leave current attributes:

.. code-block:: XML

    <propertiesBasedSyncOptions>
        <mainIdentifier>"uid=" + srcBean.getDatasetFirstValueById("uid") + ",ou=Sample,dc=lsc-project,dc=org"</mainIdentifier>
        <defaultDelimiter>;</defaultDelimiter>
        <defaultPolicy>FORCE</defaultPolicy>
        <dataset>
            <name>objectClass</name>
            <policy>FORCE</policy>
            <forceValues>
                <string>"inetOrgPerson"</string>
                <string>"organizationalPerson"</string>
                <string>"person"</string>
                <string>"top"</string>
            </forceValues>
            <delimiter>,</delimiter>
        </dataset>
        <dataset>
            <name>userPassword</name>
            <policy>KEEP</policy>
            <createValues>
                <string>"changethis"</string>
            </createValues>
        </dataset>
    </propertiesBasedSyncOptions>

Here the rules are quite simple:

* Create the objectClass attribute (class inetOrgPerson)
* Create the password with the default value "changethis"
* Copy all other attributes from source to destination (uid, cn, sn, givenname and mail) 

Check configuration
===================

Check your configuration:

.. code-block:: console

    $ bin/lsc -v

Launch synchronization
======================

Finally launch the synchronization:

.. code-block:: console

    $ bin/lsc -s all -c all

You should see the following result:

.. code-block::

    avr. 23 22:38:35 - DEBUG - Loading XML configuration from: /home/clement/tmp/lsc-2.0-SNAPSHOT/bin/../etc/lsc.xml
    avr. 23 22:38:35 - INFO  - Logging configuration successfully loaded from /home/clement/tmp/lsc-2.0-SNAPSHOT/bin/../etc/logback.xml 
    avr. 23 22:38:35 - INFO  - LSC configuration successfully loaded from /home/clement/tmp/lsc-2.0-SNAPSHOT/bin/../etc/
    avr. 23 22:38:35 - INFO  - Connecting to LDAP server ldap://localhost:33389/dc=lsc-project,dc=org as cn=Directory Manager
    avr. 23 22:38:36 - INFO  - Starting sync for user
    avr. 23 22:38:36 - INFO  - # Adding new object uid=jdoe,ou=Sample,dc=lsc-project,dc=org for user
    dn: uid=jdoe,ou=Sample,dc=lsc-project,dc=org
    changetype: add
    uid: jdoe
    mail: jdoe@example.com
    sn: Doe
    cn: John Doe
    userPassword: changethis
    objectClass: organizationalPerson
    objectClass: person
    objectClass: inetOrgPerson
    objectClass: top
    
    avr. 23 22:38:36 - INFO  - All entries: 1, to modify entries: 1, modified entries: 1, errors: 0
    avr. 23 22:38:36 - INFO  - Starting clean for user
    avr. 23 22:38:36 - INFO  - All entries: 1, to modify entries: 0, successfully modified entries: 0, errors: 0

.. tip::

    For further synchronizations, you will need to reimport fresh data from a CSV file into HSQLDB and launch the connector again.

