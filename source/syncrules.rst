*********************
Synchronization Rules
*********************

Properties based synchronization options (lsc>tasks>task>propertiesBasedSyncOptions)
====================================================================================

You can describe synchronization options through the corresponding task subnode ``lsc>tasks>task>syncOptions`` :

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <.../>
          <propertiesBasedSyncOptions>
            <mainIdentifier>...</mainIdentifier>
            <defaultDelimiter>...</defaultDelimiter>
            <defaultPolicy>...</defaultPolicy>
          </propertiesBasedSyncOptions>
          <.../>
        </task>
      </tasks>
    </lsc>

You may include various information there:

* main identifier
* conditions
* default delimiter
* default policy
* attributes description
* implementation bean

Main identifier (lsc>tasks>task>propertiesBasedSyncOptions>mainIdentifier)
--------------------------------------------------------------------------

You must provide there a main identifier construction rule. Depending on the referential you are writing to, this may have a really strong importance or not: in LDAP, OSes,... it is really important whereas this has not any meaning if you plan to populate a database.

Take this LDAP case for example:

* the main identifier will be the distinguished name,
* you want to use the email value you catch inside the datasource to create the RDN inside the ou=People branch

.. code-block:: XML

    <mainIdentifier>
        "mail=" + srcBean.getDatasetFirstValueById("email") + ",ou=People,dc=lsc-project,dc=org"
    </mainIdentifier>

You can use Javascript and you have access to the ``srcBean`` and ``dstBean``.

If this is meaning less for the referential you plan to write to, just copy the source value:

.. code-block:: XML

    <mainIdentifier>
        srcBean.getMainIdentifier()
    </mainIdentifier>

If you have an "update only" task, and the DN cannot be inferred from the source referrential, you can also use the dstBean:

.. code-block:: XML

    <mainIdentifier>
        dstBean.getMainIdentifier()
    </mainIdentifier>

Attributes (lsc>tasks>task>propertiesBasedSyncOptions>dataset)
--------------------------------------------------------------

This is the node describing how to handle each attribute:

.. code-block:: XML

    <dataset>
      <name>objectClass</name>
      <policy>KEEP</policy>
      <createValues>
        <string>"user"</string>
        <string>"top"</string>
      </createValues>
    </dataset>

Default delimiter (lsc>tasks>task>propertiesBasedSyncOptions>defaultDelimiter)
------------------------------------------------------------------------------

This is the value used to split multiple values when are provided as a single one.

Each attribute definition (see below) can define its own delimiter, but this attribute define the default one:

.. code-block:: XML

    <defaultDelimiter>;</defaultDelimiter>


Default policy (lsc>tasks>task>propertiesBasedSyncOptions>defaultPolicy)
------------------------------------------------------------------------

This is the value used to determine how to synchronize attributes value in destination, according to existing values, provided default, forced and create values.

It may take the following values : ``FORCE``, ``KEEP`` or ``MERGE``

.. code-block:: XML

    <defaultPolicy>FORCE</defaultPolicy>


Bean value (lsc>tasks>task>propertiesBasedSyncOptions>bean)
-----------------------------------------------------------

This value can be customized with a specific bean but includes a default value: ``org.lsc.beans.SimpleBean``.

This parameter points to a class that is used by LSC as a simple object storing values in a neutral way: table of named datasets which contain values.

Conditions (lsc>tasks>task>propertiesBasedSyncOptions>conditions)
-----------------------------------------------------------------

Depending on your needs, you may want to achieve only some types of operation. 

LSC is handling four different types of action : create, update, delete and change main identifier. 

You can provide on a source and destination objects basis a rule to decide if you want to achieve any or all of the operation.

The following conditions samples are written in JavaScript:

.. code-block:: XML

    <conditions>
      <create>
          1 &gt; 0
      </create>
      <update>
          srcBean.getDatasetFirstValueById('updateTimeStamp') &gt; dstBean.getDatasetFirstValueById('updateTimeStamp')
      </update>
      <delete>
          false
      </delete>
      <changeId>
          false
      </changeId>
    </conditions>

.. note::

    Do not forget to escape special characters and use XML entities (``&gt;`` for >, ``&lt;`` for <, ...), or use CDATA (see :doc:`XML file overview <overview>`).

* In the condition **create** you have access to the ``srcBean``.
* In the condition **update** you have access to the ``srcBean`` and ``dstBean``.
* In the condition **delete** you have access to the ``dstBean``.
* In the condition **change main identifier** you have access to the ``srcBean`` and ``dstBean``.

Functionality matrix
====================

Depending on the **policy** configured for an attribute, the different ``*Values`` specified and the values in the source for this attribute name, LSC's behavior varies:

+------------------------+---------------------------------------------+---------------------------------------------+---------------------------------------------+
|                        | policy=KEEP                                 | Policy=FORCE                                | Policy=MERGE                                |
+========================+=============================================+=============================================+=============================================+
| **<createValues/>**    | If:                                                                                       | If:                                         |
|                        |                                                                                           |                                             |
|                        |   * no ``<forceValues/>`` are specified,                                                  |   * no ``<forceValues/>`` are specified,    |
|                        |   * no values are read from the source,                                                   |   * and a new entry is being added,         |
|                        |   * and a new entry is being added,                                                       |                                             |
|                        |                                                                                           | the attribute will be created with          |
|                        | the attribute will be created with                                                        |                                             |
|                        |                                                                                           | values from ``<createValues/>``             |
|                        | values from ``<createValues/>``                                                           |                                             |
|                        |                                                                                           | and values from the source                  |
+------------------------+---------------------------------------------+---------------------------------------------+---------------------------------------------+
| **<defaultValues/>**   | If:                                                                                       | If:                                         |
|                        |                                                                                           |                                             |
|                        |   * no ``<forceValues/>`` are specified,                                                  | * no ``<forceValues/>`` are specified,      |
|                        |   * no values are read from the source,                                                   |                                             |
|                        |   * and the attribute doesn't yet exist in the destination,                               | values from ``<defaultValues/>`` are added, |
|                        |                                                                                           |                                             |
|                        | it is created in an existing entry                                                        | as well as any source values,               |
|                        |                                                                                           |                                             |
|                        | with values from ``<defaultValues/>``                                                     | to existing destination values              |
|                        |                                                                                           |                                             |
|                        |                                                                                           |                                             |
|                        |                                                                                           |                                             |
+------------------------+---------------------------------------------+---------------------------------------------+---------------------------------------------+
| **<forceValues/>**     | No changes are applied                      | All existing values are replaced            | Values from ``<forceValues/>`` are added    |
|                        |                                             |                                             |                                             |
|                        |                                             | with values from ``<forceValues/>``,        | to existing values in the destination,      |
|                        |                                             |                                             |                                             |
|                        |                                             | regardless of values from source            | regardless of values from source            |
+------------------------+---------------------------------------------+---------------------------------------------+---------------------------------------------+






