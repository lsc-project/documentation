********************************
LDAP connection (ldapConnection)
********************************

This section describe the LDAP connection settings. 

Let's look at a sample:

.. code-block:: XML

    <lsc>
      <connections>
        <ldapConnection>
          <name>ldap-dst-conn</name>
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
        </ldapConnection>
      </connections>
    </lsc>

First, to be able to reference the connection in the service, it's mandatory to give it a unique name element so that it can be referenced later in services (here ``ldap-dst-conn``).

* **url**: mandatory, this field shall contain a valid `LDAP URL <http://www.ietf.org/rfc/rfc2255.txt>`__
* **username**: optional, this element shall contain the string that is used to identify against the directory, most of the time it is a *LDAP Distinguished Name*, but it may be a simple SASL username (like ``DOMAIN\username`` for Active Directory)
* **password**: optional, this is the clear text value that is used to connect to the directory
* **authentication**: mandatory, one of the ``NONE``, ``SIMPLE``, ``SASL``, ``DIGEST-MD5``, ``GSSAPI`` value. ``SIMPLE`` is the value to use if you plan to authenticate through a simple username and password without any further security consideration
* **referral**: mandatory, one of the following values: ``IGNORE``, ``THROUGH``, ``THROW`` or ``ERROR``. Most of the time you should either ``IGNORE`` them, go ``THROUGH`` or handle them as an ``ERROR``

.. tip::

    By default, when referral is set to ``IGNORE``, `JNDI <https://en.wikipedia.org/wiki/Java_Naming_and_Directory_Interface>`__ adds *ManageDsaIT control*, preventing attribute computation. This can be unhandy, especially when synchronizing dynamic groups. If you set referral to ``THROW``, the *ManageDsaIT* is not sent any more.

* **derefAliases**: mandatory, one of the following values: ``NEVER``, ``SEARCH``, ``FIND`` or ``ALWAYS``. Most of the time, use ``NEVER``
* **version**: mandatory, either ``VERSION_2`` or ``VERSION_3``. Prefer ``VERSION_3`` as LDAPv2 is deprecated 
* **pageSize**: mandatory, specify either ``-1`` or the page size to use. The page size is implemented either through the corresponding control or through `Virtual List View <https://datatracker.ietf.org/doc/html/draft-ietf-ldapext-ldapv3-vlv>`__ depending on the directory capabilities which are discovered at run time (VLV preferred)
* **factory**: mandatory, only the Sun factory is supported now and you should use the corresponding value ``com.sun.jndi.ldap.LdapCtxFactory``
* **tlsActivated**: mandatory, enable or disable startTLS operation (either true or false). See :doc:`SSL/TLS <ssltls>` configuration for more information

.. note::

    For complete SSL/TLS configuration, please look at the following page: :doc:`SSL and TLS activation <ssltls>`

* **saslMutualAuthentication**: optional, set to true for SASL mutual authentication (default: false)
* **sortedBy**: optional, attribute name on which results will be sorted (server side sorting)
* **binaryAttributes**: optional, list of attribute names to be fetched as binary
* **recursiveDelete**: optional, set to true to allow recursive delete in the clean phase (default: false)
* **relaxRules**: optional, set to true to use `relax control <https://datatracker.ietf.org/doc/html/draft-zeilenga-ldap-relax>`__ (default: false)
* **saslQopType**: optional, see :doc:`Kerberos (GSSAPI) <kerberos>`

