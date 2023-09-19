**************************************
Synchronize posixGroup to groupOfNames
**************************************

Presentation
============

In a standard LDAP directory, you can have several kind of groups, each one represented by a specific object class, for example:

* ``posixGroup``
* ``groupOfNames``
* ``groupOfUniqueNames``
* ``groupOfURLs``

You may need to have both of them, for example posixGroup and groupOfNames, as posixGroup entries will be used for system authentication (PAM or SSSD) and groupOfNames for web applications authorizations.

This tutorial will explain how to sync members between a posixGroup entry and a groupOfNames entry, so you just have to edit one entry to manage membership.

Let's go
========

This tutorial is an adaptation of :doc:`Synchronize OpenLDAP groups to Active Directory groups <synchronizegroups>`, with these differences:

* We sync posixGroup to groupOfNames instead of groupOfUniqueNames to AD group
* There is only one directory (so only one connection), groups can be in different branches or in the same one
* We suppose that all users are in ou=users branch, and that the RDN is based on uid attribute

Task
----

Create a task ``group`` as a standard LSC task:

.. code-block:: XML

    <task>
        <name>group</name>
        <bean>org.lsc.beans.SimpleBean</bean>
        
        ...
        
    </task>


Services
--------

We define here a source service that will read POSIX groups, and a destination service that will manage standard groups:

.. code-block:: XML

    <ldapSourceService>
        <name>group-source-service</name>
        <connection reference="openldap" />
        <baseDn>ou=groups,dc=example,dc=com</baseDn>
        <pivotAttributes>
            <string>cn</string>
        </pivotAttributes>
        <fetchedAttributes>
            <string>cn</string>
            <string>memberUid</string>
        </fetchedAttributes>
        <getAllFilter><![CDATA[(objectClass=posixGroup)]]></getAllFilter>
        <getOneFilter><![CDATA[(&(objectClass=posixGroup)(cn={cn}))]]></getOneFilter>
        <cleanFilter><![CDATA[(&(objectClass=posixGroup)(cn={cn}))]]></cleanFilter>
    </ldapSourceService>
    <ldapDestinationService>
        <name>group-dst-service</name>
        <connection reference="openldap" />
        <baseDn>ou=groups,dc=example,dc=com</baseDn>
        <pivotAttributes>
            <string>cn</string>
        </pivotAttributes>
        <fetchedAttributes>
            <string>cn</string>
            <string>member</string>
            <string>objectClass</string>
        </fetchedAttributes>
        <getAllFilter><![CDATA[(objectClass=groupeOfNames)]]></getAllFilter>
        <getOneFilter><![CDATA[(&(objectClass=groupOfNames)(cn={cn}))]]></getOneFilter>
    </ldapDestinationService>

Properties
----------

We will now define properties:

.. code-block:: XML

    <propertiesBasedSyncOptions>
    ...  
    </propertiesBasedSyncOptions>

Main properties
^^^^^^^^^^^^^^^

We define mainIdentifier and conditions:

.. code-block:: XML

    <mainIdentifier>js:"cn=" + javax.naming.ldap.Rdn.escapeValue(srcBean.getDatasetFirstValueById("cn").toLowerCase()) + ",ou=groups,dc=example,dc=com"</mainIdentifier>
    <defaultDelimiter>;</defaultDelimiter>
    <defaultPolicy>FORCE</defaultPolicy>
    <conditions>
        <create>true</create>
        <update>true</update>
        <delete>true</delete>
        <changeId>true</changeId>
    </conditions>

Object Class
^^^^^^^^^^^^

We force the values of objectClass attribute:

.. code-block:: XML

    <dataset>
        <name>objectclass</name>
        <policy>KEEP</policy>
        <createValues>
            <string>"groupOfNames"</string>
            <string>"top"</string>
        </createValues>
    </dataset>

member
^^^^^^

We convert uid values into DN:

.. code-block:: XML

    <dataset>
        <name>member</name>
        <policy>FORCE</policy>
        <forceValues>
            <string>
            <![CDATA[rjs:
                var membersSrcUid = srcBean.getDatasetValuesById("memberUid");
                var membersDstDn = new java.util.ArrayList();
                for  (var i=0; i<membersSrcUid.size(); i++) {
                    var memberSrcUid = membersSrcUid.get(i);
                    var dn = "uid=" + memberSrcUid + ",ou=users,dc=example,dc=com";
                    membersDstDn.add(dn);
                }
                membersDstDn
            ]]>
            </string>
        </forceValues>
    </dataset>

Some explanations on this script:

- We get uid of members in source in ``membersSrcUid``
- For each value, the corresponding DN is built with a simple concatenation
- The DN is inserted into ``membersDstDn`` array
- The ``membersDstDn`` array is returned to LSC

