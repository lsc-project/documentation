*******************************
Event based LDAP source service
*******************************

This source service uses LDAP vendors capabilities or a time based pulling system to look for updates inside a directory.

This LSC plugin enables event-based updates handling upon supported directories based on `LDAP Persistent search control <https://datatracker.ietf.org/doc/html/draft-ietf-ldapext-psearch>`__ and `LDAP Content Synchronization Operation <https://datatracker.ietf.org/doc/html/rfc4533>`__.

It supports :

* through LDAP Persistent Search Control : 

    * Netscape Directory Server, 
    * Sun Directory Server (native and Java edition), 
    * Oracle Directory server, 
    * OpenDS and OpenDJ

* through LDAP Content Synchronization Control : 

    * OpenLDAP 2.4+
    * Apache DS (to be fully checked)

* through proprietary Microsoft control : Active Directory

.. important::

    The support for Active Directory is limited. Indeed, AD only accepts persistent search with the filter ``(objectClass=*)``. See the `LDAP Server Notification control <https://learn.microsoft.com/fr-fr/previous-versions/windows/desktop/ldap/ldap-server-notification-oid>`__ for more information.

It has exactly the same parameters as a LDAP source service requires, plus the ``<serverType>`` element, as shown below:

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <asyncLdapSourceService>
            <name>openldap-source-service</name>
            <connection reference="ldap-src-conn" />
            <baseDn>ou=People,dc=lsc-project,dc=org</baseDn>
            <pivotAttributes>
              <string>mail</string>
            </pivotAttributes>
            <fetchedAttributes>
              <string>description</string>
              <string>cn</string>
              <string>sn</string>
              <string>userPassword</string>
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
            <synchronizingAllWhenStarting>true</synchronizingAllWhenStarting>
            <serverType>OpenLDAP</serverType>
          </asyncLdapSourceService>
        </task>
      </tasks>
    </lsc>

The generic settings are described there: :doc:`LDAP source service <service_source_ldap>`

The specific settings are:

* **synchronizingAllWhenStarting**: set to false to bypass a full synchronization on start (default: true)
* **serverType**: one the following values:

    * ``OpenLDAP``
    * ``ApacheDS``
    * ``OpenDS``
    * ``OpenDJ``
    * ``OracleDS``
    * ``SunDS``
    * ``NetscapeDS``
    * ``ActiveDirectory``
    * ``NovellEDirectory``

