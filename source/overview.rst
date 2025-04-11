************************************
General overview of the lsc.xml file
************************************

The main configuration file
===========================

Configuration parameters described here must be placed in the ``lsc.xml`` file, located in either:

* The directory specified by the ``-f`` command line parameter to LSC, generally the etc sub directory
* The etc sub-directory if you launch LSC with the java command and the ``org.lsc.Launcher`` class

General rules about this file:

* Parameter order is important! Please respect the order given in examples.
* Characters contained between ``<!--``  and ``-->`` are comments
* `XML entities <http://en.wikipedia.org/wiki/List_of_XML_and_HTML_character_entity_references#Predefined_entities_in_XML>`__ like ``&`` should be converted or enclosed in CDATA, for example:

.. code-block:: XML

    <getOneFilter>
        (&amp;(objectClass=inetOrgPerson)(uid={uid}))
    </getOneFilter>

or:

.. code-block:: XML

    <getOneFilter>
        <![CDATA[
            (&(objectClass=inetOrgPerson)(uid={uid}))
        ]]>
    </getOneFilter>

XML namespace
=============

The XML namespace must be declared in ``lsc.xml``. This namespace will vary with LSC releases, so keep it up to date in your configuration files:

.. code-block:: XML

    <?xml version="1.0" ?>
    <lsc xmlns="http://lsc-project.org/XSD/lsc-core-2.2.xsd" revision="0">

    </lsc>

Environment variables
=====================

You can use environment variables in the configuration file:

.. code-block:: XML

        <ldapConnection id="${LDAP_CONNECTION_ID}">
            <name>${LDAP_CONNECTION_NAME}</name>
            <url>ldap://${LDAP_CONNECTION_HOST}:${LDAP_CONNECTION_PORT}/dc=lsc-project,dc=org</url>
            <username>${LDAP_CONNECTION_USERNAME}</username>
            <password>${LDAP_CONNECTION_PASSWORD}</password>
            <authentication>SIMPLE</authentication>
            <referral>IGNORE</referral>
            <derefAliases>NEVER</derefAliases>
            <version>VERSION_3</version>
            <pageSize>-1</pageSize>
            <factory>com.sun.jndi.ldap.LdapCtxFactory</factory>
            <tlsActivated>false</tlsActivated>
        </ldapConnection>

Sections
========

Settings are divided into the following sections:

* :doc:`lsc>connections <connections>`: all the settings required to identify the way connections must be established (URL, login, ...) 
* :doc:`lsc>tasks <tasks>`: various properties describing the way a source service may be synchronized with a destination service. In order to achieve this, source and destination services are wrapped to the available connections, and synchronization options are specified (transform and set values)
* :doc:`lsc>security>encryption <encryption>`: specify the security options. Currently, there are only encryption options

