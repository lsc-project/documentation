******************************************************
Synchronize OpenLDAP groups to Active Directory groups
******************************************************

Presentation
============

It is often needed to synchronize groups from one repository to another.

The difficulty is that the user identifier in the source repository is different from the one in the destination. So simply copying the values of the "member" attribute is not enough: you need to search for each value in the source member attribute the correct value for the destination. That is where the JNDI utilities of LSC can help you.

.. note::

    Be sure to know how ``lsc.xml`` file works before using this tutorial.

Let's go
========

This tutorial explain how to synchronize groups from an OpenLDAP directory to Active Directory. It should be easy to adapt to your own needs.

Task
----

Create a task "group" as a standard LSC task:

.. code-block:: XML

    <task>
        <name>group</name>
        <bean>org.lsc.beans.SimpleBean</bean>
        
        ...
        
    </task>

Services
--------

We define here a source service that will read OpenLDAP groups, and a destination service that will manage AD groups. OpenLDAP groups are ``groupsOfUniqueNames`` with ``uniqueMember`` attribute, AD groups are ``group`` with ``member`` attribute. We choose to keep ``cn`` as RDN on both sides:

.. code-block:: XML

    <asyncLdapSourceService>
        <name>group-source-service</name>
        <connection reference="openldap" />
        <baseDn>ou=groups,dc=example,dc=com</baseDn>
        <pivotAttributes>
            <string>cn</string>
        </pivotAttributes>
        <fetchedAttributes>
            <string>cn</string>
            <string>description</string>
            <string>uniqueMember</string>
        </fetchedAttributes>
        <getAllFilter><![CDATA[(objectClass=groupOfUniqueNames)]]></getAllFilter>
        <getOneFilter><![CDATA[(&(objectClass=groupOfUniqueNames)(cn={cn}))]]></getOneFilter>
        <cleanFilter><![CDATA[(&(objectClass=groupOfUniqueNames)(cn={cn}))]]></cleanFilter>
        <serverType>OpenLDAP</serverType>
    </asyncLdapSourceService>
    <ldapDestinationService>
        <name>group-dst-service</name>
        <connection reference="ad" />
        <baseDn>OU=groups,OU=demo,DC=example,DC=com</baseDn>
        <pivotAttributes>
            <string>cn</string>
        </pivotAttributes>
        <fetchedAttributes>
            <string>cn</string>
            <string>description</string>
            <string>member</string>
            <string>objectClass</string>
        </fetchedAttributes>
        <getAllFilter><![CDATA[(objectClass=group)]]></getAllFilter>
        <getOneFilter><![CDATA[(&(objectClass=group)(cn={cn}))]]></getOneFilter>
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

    <mainIdentifier>js:"cn=" + javax.naming.ldap.Rdn.escapeValue(srcBean.getDatasetFirstValueById("cn")) + ",OU=groups,OU=demo,DC=example,DC=com"</mainIdentifier>
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

We force the values of objectClass attribute in AD:

.. code-block:: XML

    <dataset>
        <name>objectclass</name>
        <policy>KEEP</policy>
        <createValues>
            <string>"group"</string>
            <string>"top"</string>
        </createValues>
    </dataset>

member
^^^^^^

Here is the tricky part: a javascript code that will parse source members and transform them into destination members:

.. code-block:: XML

    <dataset>
        <name>member</name>
        <policy>FORCE</policy>
        <forceValues>
            <string>
            <![CDATA[rjs:
                var membersSrcDn = srcBean.getDatasetValuesById("uniqueMember");
                var membersDstDn = [];
                for  (var i=0; i<membersSrcDn.size(); i++) {
                    var memberSrcDn = membersSrcDn.get(i);
                    var uid = "";
                    try {
                        uid = srcLdap.attribute(memberSrcDn, "uid").get(0);
                    } catch(e) {
                        continue;
                    }
                    var destDn = ldap.search("ou=users,ou=demo", "(sAMAccountName=" + uid + ")");
                    if (destDn.size() == 0 || destDn.size() > 1) {
                        continue;
                    }
                    var destMemberDn = destDn.get(0) + "," +  ldap.getContextDn();
                    membersDstDn.push(destMemberDn);
                }
                membersDstDn
            ]]>
            </string>
        </forceValues>
    </dataset>

Some explanations on this script:

- We get DN of members in source in ``membersSrcDn``
- For each value, a search is done in source LDAP (``srcLdap``) to get its uid
- A search is done on destination LDAP (``ldap``) on sAMAccountName to get the DN corresponding to the uid value (sAMAccountName and uid are the pivot values of the user task)
- The DN found is computed with context DN (``ldap.getContextDn``) to get the real DN
- The DN is inserted into ``membersDstDn`` array
- The ``membersDstDn`` array is returned to LSC


