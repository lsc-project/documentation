***************************************
Convert recursive groups to flat groups
***************************************

Presentation
============

This tutorial explains how to synchronize recursive groups to flat groups in an LDAP directory. It will just focus on the script needed to do it, not on how to configure LDAP connection and filters.

.. tip::

    You can use the script to synchronize groups from one directory to another, or use the same directory in source and destination to provide flat groups to applications that can't use groups of groups.

Script content
==============

This script should be put as separated js file and included in LSC taks, like explained in :doc:`scripting documentation page <scripting>`.

To sum up, the script reads members of a group. If the member's DN is inside group branch, then it calls again the method to find sub group members. It the member's DN is inside user branch, then it adds the DN value to the destination value (and converts this value to the correct DN value expected in the destination directory).

.. code-block:: console

    # vi /etc/lsc/lib.js

.. code-block:: js

    function getMembersDn(srcLdap, dstLdap, dn, groupsRegistry) {
        // Manage forward slash in DN
        dn = dn.replace(/\//g,"\\2F");

        var result = new java.util.ArrayList();

        // Check if object is a group
        var ocs = srcLdap.attribute(dn, "objectClass");
        var isGroup = false;

        for   (var i=0; i<ocs.size(); i++) {
            var oc = ocs.get(i);
            if (oc == "group") { isGroup = true; }
        }

        if ( isGroup ) {

            // Check groups registry to prevent infinite loops
            if ( groupsRegistry.contains(dn) ) {
                return result;
            } else {
                groupsRegistry.add(dn);
            }

            // Browse group to find members
            var membersDn = new java.util.ArrayList();;
            try {
                membersDn = srcLdap.attribute(dn, "member");
            } catch(e) {
                return result;
            }

            for  (var i=0; i<membersDn.size(); i++) {
                var memberDn = membersDn.get(i);
                result.addAll( getMembersDn(srcLdap,dstLdap,memberDn,groupsRegistry) );
            }
        }

        else {

            var uid = "";
            try {
                uid = srcLdap.attribute(dn, "sAMAccountName").get(0);
            } catch(e) {
                return result;
            }

            var destDn = dstLdap.search("ou=users", "(uid=" + uid + ")");
            if (destDn.size() == 0 || destDn.size() > 1) {
                return result;
            }
            var destMemberDn = destDn.get(0) + "," +  dstLdap.getContextDn();
            result.add(destMemberDn);
        }

        return result;
    }



There are some hard-coded values in the script that you may want to change:

* ``group`` : objectClass of group entries
* ``member``: attribute name containing member DN in source LDAP
* ``sAMAccountName``: attribute name containing user identifier in source LDAP
* ``ou=users`` : RDN of the users branch in destination LDAP
* ``uid``: attribute name containing user identifier in destination LDAP

Script usage in LSC configuration
=================================

Here is an example of how to call this script inside a dataset:

.. code-block:: console

    # vi /etc/lsc/lsc.xml

.. code-block:: XML

    <dataset>
        <name>member</name>
        <policy>FORCE</policy>
        <forceValues>
            <string>
            <![CDATA[js:
                var membersSrcDn = srcBean.getDatasetValuesById("member");
                var membersDstDn = new java.util.ArrayList();
                var groupsRegistry = new java.util.ArrayList();
                for  (var i=0; i<membersSrcDn.size(); i++) {
                    var memberSrcDn = membersSrcDn.get(i);
                    membersDstDn.addAll( getMembersDn(srcLdap, ldap, memberSrcDn, groupsRegistry) );
                }
                if ( membersDstDn.size() == 0) {
                    membersDstDn.add("cn=empty");
                }
                membersDstDn
            ]]>
            </string>
        </forceValues>
    </dataset>

