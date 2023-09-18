******************
SSL/TLS activation
******************

LSC can encrypt communication with a LDAP server, using either the StartTLS operation (on standard LDAP port, 389) or via SSL/TLS (on a specific port, 636).

Configuring LSC
===============

StartTLS operation
------------------

You can use the startTLS extended operation to secure a connection on the standard 389 port. This feature is available since LSC 1.1.0.

To enable startTLS on the LDAP connection, set the following node in ``/etc/lsc/lsc.xml``:

.. code-block:: XML

    <connection>
        <.../>
        <tlsActivated>true</tlsActivated>
    </connection>


SSL/TLS
-------

You can use SSL/TLS to create a secure tunnel. This implies to use ``ldaps://`` URI in ``lsc.xml``:

.. code-block:: XML

    <connection>
        <.../>
        <url>ldaps://host.domain.org/</url>
    </connection>

Trusting the certificate
========================

Choose one of the two options below to get your server's certificate trusted.

Global use: LSC will use system-wide JVM Truststore
---------------------------------------------------

First step, add the CA certificate (which signed the LDAP server certificate) in the JVM running LSC.

This tutorial is written from `http://java.sun.com/products/jndi/tutorial/ldap/security/ssl.html#CLIENT <http://java.sun.com/products/jndi/tutorial/ldap/security/ssl.html#CLIENT>`__

First, go to the security folder of your JVM installation:

.. code-block:: console

    cd $JAVA_HOME/jre/lib/security/

Then import the CA certificate (we suppose it is in a file named ``cacert.pem``):

.. code-block:: console

    ../../bin/keytool -import -file /path/to/cacert.pem -keystore cacerts

Specific use: LSC will use its own truststore
---------------------------------------------

This is exactly the same procedure as described above. But, at the end, LSC will use its own truststore instead of the system-wide JVM truststore.

First step, copy the JVM truststore to your LSC installation:

.. code-block:: console

    cp $JAVA_HOME/jre/lib/security/cacerts /etc/lsc/

Then import the new certificate into this truststore:

.. code-block:: console

    $JAVA_HOME/bin/keytool -import -file /path/to/cacert.pem \
        -keystore /etc/lsc/cacerts

Now, edit the shell script that launches LSC synchronization tasks. We suppose this is ``/usr/bin/lsc``. Replace the following line:

.. code-block:: console

    $JAVA_HOME/bin/java -cp $CLASSPATH org.lsc.Launcher $*

by this line:

.. code-block:: console

    $JAVA_HOME/bin/java -cp $CLASSPATH \
        -Djavax.net.ssl.trustStore=$CFG_DIR/cacerts \
        -Djavax.net.ssl.trustStorePassword=changeit \
        org.lsc.Launcher $*

