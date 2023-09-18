*****************
Kerberos (GSSAPI)
*****************


Kerberos can be used to authenticate to LDAP directory. In this case, you don't need to store the connection password in ``lsc.xml``.

Here are the steps to use Kerberos with LSC.

Prerequisite
============

You need to have configured Kerberos client on your server first. It means you are able to do a kinit to get a valid ticket from the Kerberos server.

For more convenience, this howto supposes you have generated a keytab for the LSC user. It will avoid the need to do a kinit for getting a ticket. Export the keytab to ``lsc.keytab``

JAAS
====

Create ``/etc/lsc/gsseg_jaas.conf`` configuration with the following: (adapt the path to keytab)

.. code-block::

    /**
     * Login Configuration for JAAS.
     *
     */
    org.lsc.jndi.JndiServices {
      com.sun.security.auth.module.Krb5LoginModule required client=TRUE useKeyTab=true keyTab="/path/to/lsc.keytab";
    };

.. tip::

    Remove ``useKeyTab`` and ``keyTab`` parameters if you don't want a keytab for now and plan to get a ticket with kinit

Kerberos
========

Soft-link the krb5.ini to your real /etc/krb5.conf:

.. code-block:: console

    ln -s /etc/krb5.conf /etc/lsc/krb5.ini

Java options
============

You need to add some options in the java command used by LSC. You can do that by exporting JAVA_OPTS:

.. code-block:: console

    export JAVA_OPTS="$JAVA_OPTS -Djavax.security.auth.useSubjectCredsOnly=false"

.. tip::

    If you need to debug, set these additional Java options: ``-Dsun.security.jgss.debug=true -Dsun.security.krb5.debug=true``

.. tip::

    You can also edit ``/usr/bin/lsc`` to remember this option.

LSC
===

Modify the LDAP connection:

* **username**: set the Kerberos username (the realm must be in uppercase)
* **password**: set a dummy password
* **authentication**: use ``GSSAPI``
* **saslQop** (optional): The desired quality-of-protection, allowed values are:

    * ``auth`` (default value): authentication only
    * ``auth-int``: authentication plus integrity protection
    * ``auth-conf``: authentication plus integrity and confidentiality protection

Example:

.. code-block:: XML

    <ldapConnection>
        <name>adsamba4-conn</name>
        <url>ldaps://pdc.example.org:636/dc=ad,dc=example,dc=org</url>
        <username>adminlsc@EXAMPLE.ORG</username>
        <password>not_really</password>
        <authentication>GSSAPI</authentication>
        <referral>IGNORE</referral>
        <derefAliases>NEVER</derefAliases>
        <version>VERSION_3</version>
        <pageSize>1000</pageSize>
        <factory>com.sun.jndi.ldap.LdapCtxFactory</factory>
        <tlsActivated>false</tlsActivated>
        <saslQop>auth</saslQop>
    </ldapConnection>

Kerberos init
=============

If you **didn't** used a keytab, you have to get a kerberos ticket:

.. code-block:: console

    kinit adminlsc@EXAMPLE.ORG


Run LSC
=======

You can now run LSC, it will authenticate trough Kerberos.

.. note::

    This documentation was done thanks to Francesco Malvezzi and Franck Rakotonindrainy
