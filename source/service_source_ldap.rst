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
            <allEntriesFilter>
              "(objectClass=inetorgperson)"
            </allEntriesFilter>
            <oneEntryFilter>
              "(&amp;(objectClass=inetorgperson)(mail=" + pivotAttributes["mail"] + "))"
            </oneEntryFilter>
            <cleanEntryFilter>
              "(&amp;(objectClass=inetorgperson)(mail=" + pivotAttributes["mail"] + "))"
            </cleanEntryFilter>
          </ldapSourceService>
        </task>
      </tasks>
    </lsc>

The LDAP service should be configured by using the following settings:

* **name**: mandatory, it should contain any value that will uniquely identify this service regarding a task
* **connection**: this node should not contain any element, just a ``reference`` attribute which is used to look for the corresponding connection with this name (as sub-element of the connection element)
* **baseDn**: mandatory, this value provides the root distinguished name to use to look for entries
* **pivotAttributes**: mandatory, this list of string values contains all the pivot attributes that are fetched when looking for all entries to synchronize and are used to get the right filter to read every single and complete entry to synchronize
* **fetchedAttributes**: mandatory, this list of string values contains all the attributes that will be read from the source directory. You can use the special attribute ``*``: in that case, all attributes from source entry (except operational attributes) will be retrieved and considered to be written in destination.
* **allEntriesFilter**: mandatory, this filter is used to look for all entries that have to be synchronized. The filter is considered as a script that will be evaluated as described in :doc:`scripting section <scripting>`.
* **oneEntryFilter**: mandatory, this filter is used to look for a particular entry. The filter is considered as a script that will be evaluated as described in :doc:`scripting section <scripting>`. You should use the pivotAttribute key->value array for selecting the current source entry. Important: the key must be written in lowercase (for example: ``pivotAttributes["samaccountname"]`` and not ``pivotAttributes["sAMAccountName"]``)
* **cleanEntryFilter**: optional, this filter is used to look for a particular entry for the clean phase. The value will be computed to replace the corresponding **destination** pivot attributes with their value at runtime. The filter is considered as a script that will be evaluated as described in :doc:`scripting section <scripting>`. You should use the pivotAttribute key->value array for matching the **destination** pivot attribute with the corresponding source entry. Important: the key must be written in lowercase (for example: ``pivotAttributes["samaccountname"]`` and not ``pivotAttributes["sAMAccountName"]``)

.. tip::

    DEPRECATED:

    Instead of ``allEntriesFilter``, ``oneEntryFilter``, and ``cleanEntryFilter``, you could use the corresponding deprecated parameters: ``getAllFilter``, ``getOneFilter``, and ``cleanFilter``. Take care that these parameters will be removed at some point.

    The deprecated parameters are evaluated as string templates:

    1. you must not surround the filters by double-quotes (") or single quotes (')
    2. the pivotAttributes array is not available, instead you should use a placeholder: ``{pivotAttributeName}``, for example ``{mail}``
    3. the pivot placeholders must be written in lowercase.

    Example: ``<getOneFilter>(&amp;(objectClass=inetorgperson)(mail={mail}))</getOneFilter>``

* **filterAsync**: optional, filter that will be used to simulate an asynchronous task (default: ``modifytimestamp>={0}``)
* **dateFormat**: optional, date format for the above filter (default: ``yyyyMMddHHmmss'Z'``)
* **interval**: optional, interval in seconds to fetch data based on the above filter (default: 5)

