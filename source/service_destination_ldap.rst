************************
LDAP destination service
************************

The LDAP destination service allows to connect to any LDAPv2 and v3 compliant directory and to update entries.

The LDAP destination service is using a :doc:`LDAP connection <connection_ldap>` to get the various connection settings.

Let's find below a sample and the parameters description :

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <ldapDestinationService>
            <name>opends-dst-service</name>
            <connection reference="ldap-dst-conn" />
            <baseDn>ou=People</baseDn>
            <pivotAttributes>
              <string>mail</string>
            </pivotAttributes>
            <fetchedAttributes>
              <string>cn</string>
              <string>sn</string>
              <string>objectClass</string>
              <string>uid</string>
              <string>mail</string>
            </fetchedAttributes>
            <getAllFilter>
              (objectClass=inetorgperson)
            </getAllFilter>
            <getOneFilter>
              (&amp;(objectClass=inetorgperson)(mail={mail}))
            </getOneFilter>
          </ldapDestinationService>
        </task>
      </tasks>
    </lsc>

The LDAP service should be configured by using the following settings:

* **name**: mandatory, it should contain any value that will uniquely identify this service regarding a task
* **connection**: this node should not contain any element, just a ``reference`` attribute which is used to look for the corresponding connection with this name (as sub-element of the connection element)
* **baseDn**: mandatory, this value provides the root distinguished name to use for searching entries
* **pivotAttributes**: mandatory, this list of string values contains all the pivot attributes that are fetched when checking for the entry existence by looking back to the source service
* **fetchedAttributes**: mandatory, this list of string values contains all the attributes that will be synchronized according to the source object and the synchronization options
* **getAllFilter**: mandatory, this filter is used to look for all entries whom existence should be checked through the source service
* **getOneFilter**: mandatory, this filter is used to look for a particular entry - the value will be computed to replace the corresponding **source**  pivot attributes with the value from the original object at runtime

.. tip::

    The pivot attributes must be written in lowercase, and surrounded by ``{`` and ``}``

