***************************************
Extended synchronization rules for LDAP
***************************************

LSC has some built-in functions to interact with generic LDAP servers.

Performing a BIND operation to test a password
==============================================

You can check a user's password on any LDAP server by calling one of the methods described in `this JavaDoc <http://lsc-project.org/javadoc/latest/org/lsc/utils/directory/LDAP.html>`__.

For example, the following would open an anonymous LDAP connection to the ldap.example.com server, and attempt a BIND operation with the DN "uid=1234,ou=people,dc=example,dc=com" and the password "secret":

.. code-block:: js

    LDAP.canBind("ldap://ldap.example.com/", "uid=1234,ou=people,dc=example,dc=com", "secret")

More complicated, you can connect and bind as a service DN, then search for a user's DN and use that to test a password in a BIND operation:

.. code-block:: js

    LDAP.canBindSearchRebind("ldap://ldap.example.com/o=demo??sub?uid=1234", \
        "cn=svc-lsc,ou=technical,o=demo", "svc-lsc-password", "secret")

These methods all return booleans, so can be used in conditions. For example:

.. code-block:: js

    lsc>tasks>task>syncOptions>condition>update = LDAP.canBind(
        "ldap://ldap.example.com/", "uid=1234,ou=people,dc=example,dc=com", "secret")

