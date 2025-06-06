*****************************************
Populating Active Directory from OpenLDAP
*****************************************

Presentation
============

This article describes the creation of a connector which synchronizes data from OpenLDAP to Active Directory.

It has been done on a Linux machine with LSC v2. Everything will be done through the console.

We suppose that you have :doc:`LSC installed <installation>`, and OpenLDAP and AD running.

Configure your connector
========================

Create a configuration directory for your OpenLDAP to AD connector, for example:

.. code-block:: console

    # mkdir /etc/lsc/openldap2ad/

You need a logback configuration in this directory:

.. code-block:: console

    # cp /etc/lsc/logback.xml /etc/lsc/openldap2ad/

All configuration is done through the configuration file ``/etc/lsc/openldap2ad/lsc.xml``. A sample file is provided, so copy it and edit it: `https://github.com/lsc-project/lsc/blob/master/sample/ad/etc-opendj2ad/lsc.xml <https://github.com/lsc-project/lsc/blob/master/sample/ad/etc-opendj2ad/lsc.xml>`__

All configuration parameters are described in :ref:`configuration <index-configuration>`.

Destination LDAP directory connection settings (Active Directory)
-----------------------------------------------------------------

Target directory properties:

* **Destination directory authentication mode**: we choose ``simple``.
* **LDAP server URL**: a standard LDAP URI. Do not forget the trailing slash. We choose ``ldap://adlds.lsc-project.org:389/`` (not available online for test!).
* **Naming context root DN**: the data suffix. We choose ``dc=lsc-project,dc=org``.

And provide information on the identity used to bind to the target directory:

* **Principal DN**: for example ``cn=LSC,dc=lsc-project,dc=org``.
* **Password**: for example ``PassW0RD``.

.. code-block:: XML

    <lsc>
        <connections>
          <ldapConnection>
              <name>AD</name>
              <url>ldap://adlds.lsc-project.org:389/dc=lsc-project,dc=org</url>
              <username>cn=LSC,dc=lsc-project,dc=org</username>
              <password>PassW0RD</password>
              <authentication>SIMPLE</authentication>


To manage Active Directory Paged Results Control (which enables to return more than 1000 entries), add:

.. code-block:: XML

              <pageSize>1000</pageSize>

Attribute unicodePwd is a binary attribute:

.. code-block:: XML

              <binaryAttributes><string>unicodePwd</string></binaryAttributes>

And close the XML node :

.. code-block:: XML

          </ldapConnection>


As we want to act on the password (the attribute ``unicodePwd``), we must use an SSL connection to Active Directory (password modification is not allowed on a clear connection).

The steps are:

* Set and export the CA certificate used in AD (see `http://confluence.atlassian.com/display/CROWD/Configuring+an+SSL+Certificate+for+Microsoft+Active+Directory <http://confluence.atlassian.com/display/CROWD/Configuring+an+SSL+Certificate+for+Microsoft+Active+Directory>`__)
* Import the certificate in the JVM or in your own SSL truststore (see :doc:`SSL and TLS activation <ssltls>`)
* Use ldaps in the AD URI in ``lsc.xml`` (*lsc>connections>ldapConnection>url*)

Source LDAP directory settings (OpenLDAP)
-----------------------------------------

Source directory properties:

* **Source directory authentication mode**: we choose simple bind.
* **LDAP server URL**: a standard LDAP URI for our OpenLDAP server. Do not forget the trailing slash. We choose ``ldap://localhost:389/``.
* **Naming context root DN**: the data suffix. We choose ``dc=lsc-project,dc=org``.
* **Principal DN**: for example ``cn=Directory Manager,dc=lsc-project,dc=org``.
* **Password**: for example ``secret``.

.. code-block:: XML

    <ldapConnection>
        <name>openldap</name>
        <url>ldap://localhost:389/dc=lsc-project,dc=org</url>
        <username>cn=Directory Manager,dc=lsc-project,dc=org</username>
        <password>secret</password>
        <authentication>SIMPLE</authentication>
    </ldapConnection>

Task
----

You can have multiple tasks in your connector (identified by the *lsc>tasks>task* value). In this tutorial, we have only one task, named ``ADuser``.

In this task, we define the source service (OpenLDAP) and the destination service (AD):

.. code-block:: XML

    <tasks>
        <task>
            <name>adUser</name>
            <ldapSourceService>
                <name>openldap-source-service</name>
                <connection reference="openldap" />
                <baseDn>ou=People,dc=lsc-project,dc=org</baseDn>
                <pivotAttributes>
                    <string>uid</string>
                </pivotAttributes>
                <fetchedAttributes>
                    <string>cn</string>
                    <string>description</string>
                    <string>givenName</string>
                    <string>mail</string>
                    <string>sn</string>
                    <string>uid</string>
                    <string>userpassword</string>
                </fetchedAttributes>
                <getAllFilter><![CDATA[(objectClass=inetOrgPerson)]]></getAllFilter>
                <getOneFilter><![CDATA[(&(objectClass=inetOrgPerson)(uid={uid}))]]></getOneFilter>
                <cleanFilter><![CDATA[(&(objectClass=inetOrgPerson)(uid={sAMAccountName}))]]></cleanFilter>
            </ldapSourceService>
            <ldapDestinationService>
                <name>ad-dst-service</name>
                <connection reference="AD" />
                <baseDn>cn=Users,dc=lsc-project,dc=org</baseDn>
                <pivotAttributes>
                    <string>sAMAccountName</string>
                </pivotAttributes>
                <fetchedAttributes>
                    <string>cn</string>
                    <string>description</string>
                    <string>givenName</string>
                    <string>mail</string>
                    <string>objectclass</string>
                    <string>pwdLastSet</string>
                    <string>sAMAccountName</string>
                    <string>sn</string>
                    <string>unicodePwd</string>
                    <string>userAccountControl</string>
                    <string>userPrincipalName</string>
                </fetchedAttributes>
                <getAllFilter><![CDATA[(objectClass=user)]]></getAllFilter>
                <getOneFilter><![CDATA[(&(objectClass=user)(sAMAccountName={uid}))]]></getOneFilter>
            </ldapDestinationService>

We will also define how the target DN is built. Let's choose that the target DN is composed from the source's attribute ``cn`` and the destination branch ``cn=Users``.

.. code-block:: XML

    <lsc>
        <.../>
        <tasks>
            <task>
                <.../>
                <propertiesBasedSyncOptions>
                    <mainIdentifier>"cn=" + srcBean.getDatasetFirstValueById("cn") + ",cn=Users,dc=lsc-project,dc=org"</mainIdentifier>
                    <.../>
                </propertiesBasedSyncOptions>
            </task>
        </tasks>

Synchronization options
-----------------------

This is the last configuration part, but not the least, because we will now describe all our synchronization rules.

In this tutorial, we plan to use these rules:

+------------------+-----------------------+--------------------------------------+
| Source attribute | Destination attribute | Rule                                 |
+==================+=======================+======================================+
| cn               | cn                    | =                                    |
+------------------+-----------------------+--------------------------------------+
| sn               | sn                    | =                                    |
+------------------+-----------------------+--------------------------------------+
| uid              | uid                   | =                                    |
+------------------+-----------------------+--------------------------------------+
| mail             | mail                  | =                                    |
+------------------+-----------------------+--------------------------------------+
| givenName        | givenName             | =                                    |
+------------------+-----------------------+--------------------------------------+
| description      | description           | =                                    |
+------------------+-----------------------+--------------------------------------+
|                  | objectClass           | user/person/organizationalPerson/top |
+------------------+-----------------------+--------------------------------------+
| uid              | sAMAccountName        | =                                    |
+------------------+-----------------------+--------------------------------------+
| uid              | userPrincipalName     | uid + "@lsc-project.org"             |
+------------------+-----------------------+--------------------------------------+
|                  | userAccountControl    | Set as "Normal account"              |
+------------------+-----------------------+--------------------------------------+
|                  | pwdLastSet            | Force password change on creation    |
+------------------+-----------------------+--------------------------------------+
|                  | unicodePwd            | "changeit"                           |
+------------------+-----------------------+--------------------------------------+


These rules are applied through syncoptions:

.. code-block:: XML

    
    <lsc>
        <.../>
        <tasks>
            <task>
                <propertiesBasedSyncOptions>
                    <mainIdentifier>...</mainIdentifier>
                    <defaultDelimiter>;</defaultDelimiter>
                    <defaultPolicy>FORCE</defaultPolicy>
                    <conditions>
                        <create>true</create>
                        <update>true</update>
                        <delete>true</delete>
                        <changeId>true</changeId>
                    </conditions>
                    <dataset>
                        <name>objectclass</name>
                        <policy>KEEP</policy>
                        <createValues>
                            <string>"user"</string>
                            <string>"organizationalPerson"</string>
                            <string>"person"</string>
                            <string>"top"</string>
                        </createValues>
                    </dataset>
                    <dataset>
                        <name>sAMAccountName</name>
                        <policy>KEEP</policy>
                        <createValues>
                            <string>srcBean.getDatasetFirstValueById("uid")</string>
                        </createValues>
                    </dataset>
                    <dataset>
                        <!-- userPrincipalName = uid + "@lsc-project.org" -->
                        <name>userPrincipalName</name>
                        <policy>FORCE</policy>
                        <forceValues>
                            <string>srcBean.getDatasetFirstValueById("uid") + "@lsc-project.org"</string>
                        </forceValues>
                    </dataset>
                    <dataset>
                        <name>userAccountControl</name>
                        <policy>KEEP</policy>
                        <createValues>
                           <string>AD.userAccountControlSet( 0, [AD.UAC_SET_NORMAL_ACCOUNT])</string>
                        </createValues>
                    </dataset>
                    <dataset>
                        <!-- pwdLastSet = 0 to force user to change password on next connection --> 
                        <name>pwdLastSet</name>
                        <policy>KEEP</policy>
                        <createValues>
                            <string>"0"</string>
                        </createValues>
                    </dataset>
                    <dataset>
                        <!-- unicodePwd = "changeit" at creation (requires SSL connection to AD) -->
                        <name>unicodePwd</name>
                        <policy>KEEP</policy>
                        <createValues>
                            <string>AD.getUnicodePwd("changeit")</string>
                        </createValues>
                    </dataset>
                </propertiesBasedSyncOptions>
            </task>
        </tasks>
    </lsc>

You can test your synchronization in "dry-run" mode (``-n``), which means no modification is done on the target directory:

.. code-block:: console

    # /usr/bin/lsc -f /etc/lsc/openldap2ad -s all -c all -n

If the test is successful, you can run it with modifications applied:

.. code-block:: console

    # /usr/bin/lsc -f /etc/lsc/openldap2ad -s all -c all


