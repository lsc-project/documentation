*******************
XML persistence map
*******************

To use LSC synchronisation with databases you have to define how LSC will retrieve data from the :doc:`source database <service_source_database>` or how it will write them to :doc:`destination database <service_destination_database>`. This will be done by specifying IBatis persistence map in XML.

Introduction
============

The XML persistence map makes correspondance between fields found in SQL tables and LSC Bean attributes. It should be be placed in the ``/etc/lsc/sql-map-config.d/`` directory.

This file is divided in different sections:

* Definition of SQL fields and LSC Bean attributes mapping
* Definition of SQL requests to retrieve data
* Definition of SQL requests to write data


Mapping definition
==================

Retrieving data with SQL queries
--------------------------------

SQL requests are defined in a second part of this file. Each SQL request is defined through the ``select`` XML tag. This tag could contain four parameters:

* **id** specifies the identifier of this request, called later by LSC after configuration in ``lsc.xml``
* **parameterClass**: if a ``resultMap`` attribute is specified, then you have to also specify this parameter that will contains:

    * ``java.util.HashMap`` for a query that retrieves a identifier list;
    * ``java.util.map`` for a query that retrieves data corresponding to an identifer;

* **resultClass**: this will contain the Java object to receive data: this should be ``java.util.HashMap``
* **resultMap**: instead of ``resultClass``, you could specify this parameter that specifies the ID of a resultMap you want to use to match SQL field, typically the one used to map SQL field and LDAP attributes. See :ref:`persistence-attributes-mapping` below.

With ``select``, three basic requests can be declared:

* one for retrieving all identifiers, it is recommended that the identifier match the value specified by the property ``lsc>tasks>TASKNAME>destination>pivotAttrs`` in the configuration file
* one for retrieving data corresponding to an identifier, you need the identifier to match the value specified by the property ``lsc>tasks>TASKNAME>destination>pivotAttrs`` in the configuration file
* one for retrieving data corresponding to an identifier for clean phase, you need the identifier to match the value specified by the property ``lsc>tasks>TASKNAME>destination>pivotAttrs`` in the configuration file

For example, suppose a SQL table named "users" like the following:

+------+----------+-----------+-------------+-----------------------+----------+
| ID   | LASTNAME | FIRSTNAME | NAME        | EMAIL                 | PASSWORD |
+======+==========+===========+=============+=======================+==========+
| jdoe | Doe      | John      | John        | jdoe@lsc-project.org  | jdoe123  |
+------+----------+-----------+-------------+-----------------------+----------+
| test | Test     | sample    | sample Test | stest@lsc-project.org | stestabc |
+------+----------+-----------+-------------+-----------------------+----------+


Then, the first SQL request to retrieve all identifiers should be:

.. code-block:: XML

    <select id="getInetOrgPersonList" resultClass="java.util.HashMap">
      SELECT id as uid
      FROM users
    </select>

Then, the second one should be:

.. code-block:: XML

    <select id="getInetOrgPerson" resultClass="java.util.HashMap"
            parameterClass="java.util.Map">
      SELECT id as uid,
             lastname as sn,
             firstname as givenName,
             name as cn,
             email as mail,
             password as userPassword
      FROM users
      WHERE id = #uid#
    </select>


And for the clean phase it should be:

.. code-block:: XML

    <select id="getInetOrgPersonClean" resultClass="java.util.HashMap"
            parameterClass="java.util.Map">
      SELECT id as uid
      FROM users
      WHERE id = #uid#
    </select>

.. tip::

    The ``#uid#`` is the SQL field returned by the first SQL query. So, if the first SQL query selects two field (``SELECT name, email``), then you can use these two field to retrieve a user (``#name#`` and ``#email#``).

.. important::

    The ``#xxx#`` field must be in lowercase.

Writing data with SQL queries
-----------------------------

You need SQL queries to write data if you configure a :doc:`destination database service <service_destination_database>`.

You can write 3 types of queries:

* Insert queries, to add data
* Update queries, to modify existing data
* Delete queries, to delete data

.. tip::

    For each type, you can provide more thant one SQL query, each one will be exectuted by LSC.

Insert query
^^^^^^^^^^^^

.. code-block:: XML

    <insert id="insertInetOrgPerson" parameterClass="java.util.Map">
        INSERT INTO inetorgperson
            ( uid, sn, givenname, cn, mail, address, telephonenumber)
            VALUES ( #uid#, #sn#, #givenname#, #cn#, #mail#, #address#, #telephonenumber# )
    </insert>

Update query
^^^^^^^^^^^^

.. code-block:: XML

    <update id="updateInetOrgPerson" parameterClass="java.util.Map">
        UPDATE inetorgperson
            SET uid = #uid#, sn = #sn# , givenname = #givenname#, cn = #cn#, address = #address#, telephonenumber = #telephonenumber#
            WHERE mail = #mail#
    </update>

Delete query
^^^^^^^^^^^^

.. code-block:: XML

    <delete id="deleteInetOrgPerson" parameterClass="java.util.Map">
        DELETE FROM inetorgperson
            WHERE mail = #mail#
    </delete>

.. _persistence-attributes-mapping:

Attributes mapping
------------------

This step is optional. If you don't want to use the syntax ``SELECT field AS attribute ...``, use this. Otherwise, skip this section.

The mapping is declared through the ``resultMap`` XML tag. This tag takes two parameters:

* **id** is defined by the LDAP object class suffixed by the "Result" string, the first character must be in upper case;
* **class** contains the JAVA object class, the first character must be in upper case.

Mapping is done by the ``result`` XML tag found in ``resultMap``. This tag takes two parameters:

* **property** defines the attribute to be mapped into;
* **column** defines the SQL field name.

The mapping definition should exactly fit fields returned by the SQL query that returns data.

For example, consider the ``inetOrgPerson`` LDAP object class, then the associated ``resultMap`` definition could be something like the following:

.. code-block:: XML

    <resultMap id="InetOrgPersonResult" class="InetOrgPerson">
        <result property="sn" column="lastname"/>
        <result property="cn" column="name"/>
        <result property="givenName" column="firstname"/>
        <result property="userPassword" column="password"/>
        <result property="uid" column="id"/>
        <result property="mail" column="email"/>
    </resultMap>

Finalize configuration
======================

LSC uses IBatis to perform SQL/Bean mapping. So, once the XML persistence map has been created, this has to be declared in IBatis through the ``/etc/lsc/sql-map-config.xml``.

Just add a line like the following:

.. code-block:: XML

    <sqlMap url="file://${lsc.config}/sql-map-config.d/InetOrgPerson.xml"/>

You may have something like the following:

.. code-block:: XML

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE sqlMapConfig PUBLIC "-//iBATIS.com//DTD SQL Map Config 2.0//EN"
        "http://www.ibatis.com/dtd/sql-map-config-2.dtd">
    <sqlMapConfig>
    
      <properties resource="database.properties"/>
      <transactionManager type="JDBC">
        <dataSource type="SIMPLE">
          <property name="JDBC.Driver" value="${driver}"/>
          <property name="JDBC.ConnectionURL" value="${url}"/>
          <property name="JDBC.Username" value="${username}"/>
          <property name="JDBC.Password" value="${password}"/>
          <property name="Pool.MaximumActiveConnections" value="15"/>
          <property name="Pool.MaximumIdleConnections" value="15"/>
          <property name="Pool.MaximumWait" value="1000"/>
        </dataSource>
      </transactionManager>
    
      <sqlMap url="file://${lsc.config}/sql-map-config.d/InetOrgPerson.xml"/>
    
    </sqlMapConfig>

.. note::

    You need to configure ``lsc.xml`` to use these SQL requests, see :doc:`source database service <service_source_database>` or :doc:`destination database service <service_destination_database>` documentation.


