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
            <conditions>...</conditions>
            <hooks>...</hooks>
            <dataset>...</dataset>
          </propertiesBasedSyncOptions>
          <.../>
        </task>
      </tasks>
    </lsc>

You may include various information there:

* main identifier
* default delimiter
* default policy
* conditions
* hooks
* attributes description

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


Hooks (lsc>tasks>task>propertiesBasedSyncOptions>hooks)
-------------------------------------------------------

In this node, you can call a hook for each specific operations:

.. code-block:: XML

    <hooks>
      <outputFormat>ldif</outputFormat>
      <createPostHook>bin/hook-ldif.sh</createPostHook>
      <updatePostHook>bin/hook-ldif.sh</updatePostHook>
      <deletePostHook>bin/hook-ldif.sh</deletePostHook>
      <changeIdPostHook>bin/hook-ldif.sh</changeIdPostHook>
    </hooks>

* ``outputFormat``: format of the entry passed to the script. Possible values are ``ldif`` or ``json``. Default is ``ldif``.
* ``createPostHook``: path to the hook script managing each entry creation. The script will be called with 2 arguments: the DN of the entry, and the operation (``create``). The entry is passed on stdin, in the appropriate ``outputformat``
* ``updatePostHook``: path to the hook script managing each entry update. The script will be called with 2 arguments: the DN of the entry, and the operation (``update``). The entry is passed on stdin, in the appropriate ``outputformat``
* ``deletePostHook``: path to the hook script managing each entry removal. The script will be called with 2 arguments: the DN of the entry, and the operation (``delete``). Nothing is passed on stdin.
* ``changeIdPostHook``: path to the hook script managing each entry renaming. The script will be called with 2 arguments: the DN of the entry, and the operation. The entry is passed on stdin, in the appropriate ``outputformat``

Hook scripts are not supposed to write anything to stdout or stderr. If so, LSC will display theses messages as log warnings.

Here is an example of modification in ldif, passed to stdin:

.. code-block::

    # Mon Dec 11 16:39:54 CET 2023
    dn: cn=CN0001-hook,ou=ldap2ldap2TestTaskDst,ou=Test Data,dc=lsc-project,dc=org
    changetype: modify
    replace: description
    description: CN0001-hook
    -

Here is an example of modification in json, passed to stdin:

.. code-block::

    [ {
      "attributeName" : "description",
      "values" : [ "CN0001-hook" ],
      "operation" : "REPLACE_VALUES"
    }, {
      "attributeName" : "userCertificate;binary",
      "values" : [ "MIIDkTCCAnmgAwIBAgIUDhx/9qofTrT+yNFFvihdDn7rjOQwDQYJKoZIhvcNAQELBQAwWDELMAkGA1UEBhMCRlIxDTALBgNVBAgMBHRlc3QxDTALBgNVBAcMBHRlc3QxDTALBgNVBAoMBHRlc3QxDTALBgNVBAsMBHRlc3QxDTALBgNVBAMMBHRlc3QwHhcNMjMxMDI3MTQzMDQxWhcNMzMxMDI0MTQzMDQxWjBYMQswCQYDVQQGEwJGUjENMAsGA1UECAwEdGVzdDENMAsGA1UEBwwEdGVzdDENMAsGA1UECgwEdGVzdDENMAsGA1UECwwEdGVzdDENMAsGA1UEAwwEdGVzdDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKdWt0QgFnEi7a1hIJQv4ZdOM5y0GGLHQYNrUNSReArvpkYUY5zasFNVzVHCApRuj0t1NMDrn1gNzKkxTIbYGaWRSn+21J0ow+Nxh2TAQW8dkJnWTksCfyGGGItI5q3ST3EUKnepaAzUYYENcSHRyx7UY/3XuzcW0aGhy4PrVTIHBpyLq0Uzv8nH5nbWM+LYt6YbQMmlAz/psTXIC2dfEZhUb4plLGSo7rZxM5geC6Z+os+I8+uw+mGjps1VP7eGq0jCGHNs2rUHMqBNgLvwMH2WlMXo/iNarAb8fUEPdp59FwiTygBlWAn6GoKHJ1HWPpqMxdtjL2Y5+ZMcp70eJqcCAwEAAaNTMFEwHQYDVR0OBBYEFLwffjUBL/Rp4a6MgeCJiFnCZFu8MB8GA1UdIwQYMBaAFLwffjUBL/Rp4a6MgeCJiFnCZFu8MA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBACjwsg1Z9PyauoKAhkIfyPTEnlEOCo1nN37c2vnH4fyY6fuBdV6GWtk/u9FCuDmYT/4KDRxe33ZUChwSUX0INgamOarWRES3UoPC1GeOvuMf7uustEMLcHAYZVKXSZUrsOjw+VIZ5XrD6GDE64QtvW5Ve3jf43aGgLf27NF0vhF9+gHOZjjBT33S977HUutMUKfRu9PdHAn8Yb1FmSbAvqqK+SAjn6cJC8l5yS5t0BSNQGbKSA8bPzvWI9HXYVvb+ym6GDrsr+Zad3NrqUSZGzS2JFEDVD9aAikldXu6g02fA5A7nufVePmaG7iTyylO/ZU2lTiJ0SHc2DnO0pg2i+0=" ],
      "operation" : "REPLACE_VALUES"
    } ]


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






