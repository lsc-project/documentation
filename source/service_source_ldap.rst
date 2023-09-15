*******************
LDAP source service
*******************

The LDAP source service allows to connect to any LDAPv2 and v3 compliant directory and to search entries that will be synchronized.

The LDAP source service is using a :doc:`LDAP connection <connection_ldap>` to get the various connection settings.

Let's find below a sample and the parameters description :

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <ldapSourceService>
            <name>opends-src-service</name>
            <connection reference="ldap-src-conn" />
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
            <cleanFilter>
              (&amp;(objectClass=inetorgperson)(mail={mail}))
            </cleanFilter>
          </ldapSourceService>
        </task>
      </tasks>
    </lsc>

The LDAP service should be configured by using the following settings:

* **name**: mandatory, it should contain any value that will uniquely identify this service regarding a task
* **connection**: this node should not contain any element, just a ``reference`` attribute which is used to look for the corresponding connection with this name (as sub-element of the connection element)
* **baseDn**: mandatory, this value provides the root distinguished name to use to look for entries
* **pivotAttributes**: mandatory, this list of string values contains all the pivot attributes that are fetched when looking for all entries to synchronize and are used to get the right filter to read every single and complete entry to synchronize
* **fetchedAttributes**: mandatory, this list of string values contains all the attributes that will be read from the source directory
* **getAllFilter**: mandatory, this filter is used to look for all entries that have to be synchronized
* **getOneFilter**: mandatory, this filter is used to look for a particular entry - the value will be computed to replace the corresponding **source** pivot attributes with their value at runtime

.. tip::

    The pivot attributes must be written in lowercase, and surrounded by ``{`` and ``}``

* **cleanFilter**: optional, this filter is used to look for a particular entry for the clean phase - the value will be computed to replace the corresponding **destination** pivot attributes with their value at runtime

.. tip::

    The pivot attributes must be written in lowercase, and surrounded by ``{`` and ``}``

* **filterAsync**: optional, filter that will be used to simulate an asynchronous task (default: ``modifytimestamp>={0}``)
* **dateFormat**: optional, date format for the above filter (default: ``yyyyMMddHHmmss'Z'``)
* **interval**: optional, interval in seconds to fetch data based on the above filter (default: 5)

