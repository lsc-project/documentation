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
            <allEntriesFilter>
              "(objectClass=inetorgperson)"
            </allEntriesFilter>
            <oneEntryFilter>
              "(&amp;(objectClass=inetorgperson)(mail=" + pivotAttributes["mail"] + "))"
            </oneEntryFilter>
          </ldapDestinationService>
        </task>
      </tasks>
    </lsc>

The LDAP service should be configured by using the following settings:

* **name**: mandatory, it should contain any value that will uniquely identify this service regarding a task
* **connection**: this node should not contain any element, just a ``reference`` attribute which is used to look for the corresponding connection with this name (as sub-element of the connection element)
* **baseDn**: mandatory, this value provides the root distinguished name to use for searching entries
* **pivotAttributes**: mandatory, this list of string values contains all the pivot attributes that are fetched when checking for the entry existence by looking back to the source service
* **fetchedAttributes**: mandatory, this list of string values contains all the attributes that will be synchronized according to the source object and the synchronization options. You can use the special attribute ``*``: in that case, all attributes from source entry (except operational attributes) and all attributes configured as createValues, defaultValues, and forceValues in :ref:`datasets <datasets_section>` are considered to be written in destination.
* **allEntriesFilter**: mandatory, this filter is used to look for all entries that have to be synchronized. The filter is considered as a script that will be evaluated as described in :doc:`scripting section <scripting>`.
* **oneEntryFilter**: mandatory, this filter is used to look for a particular entry. The value will be computed to replace the corresponding **source** pivot attributes with their value at runtime. The filter is considered as a script that will be evaluated as described in :doc:`scripting section <scripting>`. You should use the pivotAttribute key->value array for matching the **source** pivot attribute with the corresponding destination entry. Important: the key must be written in lowercase (for example: ``pivotAttributes["samaccountname"]`` and not ``pivotAttributes["sAMAccountName"]``)

.. tip::

    DEPRECATED:

    Instead of ``allEntriesFilter`` and ``oneEntryFilter`` you could use the corresponding deprecated parameters: ``getAllFilter`` and ``getOneFilter``. Take care that these parameters will be removed at some point.

    The deprecated parameters are evaluated as string templates:

    1. you must not surround the filters by double-quotes (") or single quotes (')
    2. the pivotAttributes array is not available, instead you should use a placeholder: ``{pivotAttributeName}``, for example ``{mail}``
    3. the pivot placeholders must be written in lowercase.

    Example: ``<getOneFilter>(&amp;(objectClass=inetorgperson)(mail={mail}))</getOneFilter>``

