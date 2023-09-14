**************************************
Synchronizing to/from Active Directory
**************************************

Microsoft's Active Directory is a standards-based LDAP directory (well, mostly). Therefore, it is quite easy to set up identity synchronization with a AD Domain Controller. Below are some tips to get around some of the specifics.

Anonymous access controls
=========================

By default, anonymous access to Active Directory servers has almost no rights to read data. Create a technical account, and use that to give LSC access to the server.

Search results size limit
=========================

As most LDAP servers, Active Directory limits the maximum number of results to a search. By default, this limit is 1000. Since LSC starts by doing a search for all identities in the directory, this means a maximum of 1000 entries will be synchronized.

To work around this, LSC can use the `Paged Results control <http://www.ietf.org/rfc/rfc2696>`__. Set the following property for the source or the destination in ``lsc.xml``, and LSC will read results by page, with no maximum limit.

.. code-block:: XML

    <connection>
      <.../>
      <pageSize>1000</pageSize>
    </connection>

Multi valued attributes size limit (range)
==========================================

Active Directory limits the maximum number of values returned by a ldap search for multi-valued attribute. As an example, AD use Group objects which contains the multi-valued attribute member. 

By default this limit in AD 2008 R2 is fixed to 1500 values. This limit is also called range.

If we have groups with more than 1500 members LSC could fail to correctly synchronize these groups.

This limit is configurable in Active Directory (Windows 2008/2008 R2) using this `KB <http://support.microsoft.com/kb/2009267>`__

You can use some javascript code to get all values.

The following script can be used to bypass this limit. You can register it in an external js file (see :doc:`Scripting <scripting>`).

.. code-block:: js

    function getRangeValues(attrName) {
        var newDataset = new Array();
        var rangeStart = null;
        var rangeEnd = null;
        var rangeSize = null;
        var arrayCounter = 0;
        var tmpDataset = srcBean.getDatasetValuesById(attrName).toArray();
        if (tmpDataset.length > 0) {
            rangeStart = 0
            rangeEnd = "*"
            rangeSize = 0
        } else {
            var attrList = srcBean.getDatasetsNames().toArray()
            if (attrList != null) {
                if (attrList.length > 0) {
                    for (var i = 0; i < attrList.length; i++) {
                        if (attrList[i].toString().contains(attrName + ";range")) {
                            var startPos = null
                            var sepPos = null
                            startPos = attrList[i].toString().indexOf(";range=") + 7
                            sepPos = attrList[i].toString().indexOf("-", startPos)
                            rangeStart = attrList[i].toString().substring(startPos, sepPos)
                            rangeEnd = attrList[i].toString().substring(sepPos + 1)
                            rangeSize = Number(rangeEnd) - Number(rangeStart)
                            break
                        }
                    }
                }
            }
        }
        if (rangeStart != null) {
            var tmpAttrName = "";
            while (rangeStart != "*") {
                if (rangeStart == "0" && rangeEnd != "*") {
                    tmpAttrName = attrName + ";range=" + rangeStart.toString() + "-" + rangeEnd.toString()
                    tmpDataset = srcBean.getDatasetValuesById(tmpAttrName).toArray()
                } else if (rangeStart != "0") {
                    tmpAttrName = "member;range=" + rangeStart.toString() + "-" + rangeEnd.toString()
                    tmpDataset = srcLdap.attribute(srcBean.getMainIdentifier(), tmpAttrName).toArray()
                }
                if (rangeEnd == "*") {
                    rangeStart = "*"
                }
                if (tmpDataset != null) {
                    if (tmpDataset.length > 0) {
                        for (var i = 0; i < tmpDataset.length; i++) {
                            // Add attribute value processing logic here;
                            // Be sure to explicitly type the array elements to prevent conversion to an object array;
                            newDataset[arrayCounter++] = String(tmpDataset[i])
                        }
                        if (rangeEnd != "*") {
                            rangeStart = Number(rangeEnd) + 1
                            rangeEnd = Number(rangeStart) + Number(rangeSize)
                        }
                    } else {
                        rangeEnd = "*"
                    }
                } else {
                    rangeEnd = "*"
                }
            }
        }
        return newDataset;
    }



Filter to find user objects
===========================

Active Directory defines a **user** objectClass, which is used to represent user accounts.

So, using a LDAP filter in a search to find users is easy, just write ``(objectClass=user)``. Well, **almost**.

Actually, the **user** objectClass is also used to represent **computers** and **trust accounts** [#f1]_. So you need to add some components to your filter to avoid selecting these as well. Two options:

- Select the account type specifically: ``(&(objectClass=user)(sAMAccountType=805306368))``
- Exclude the sub-objectClasses explicitly, like this: ``(&(objectClass=user)(!(objectClass=computer)))``

Thanks to Alexey Wasilyev for this tip.

Indexing search attributes
==========================

LSC performs lots of a searches on the same attributes (those specified in *lsc>tasks>taskname>service>pivotAttrs* node in ``lsc.xml``). If this/these attributes are not indexed in Active Directory, this can make LSC seem to run very slowly.

It is easy to index an extra attribute in Active Directory. On the Schema Master domain controller:

- Open a new MMC controller: from the Start menu, choose "Execute..." and type in "mmc", then hit enter.
- From the File menu, select "Add a component" and scroll down to "Active Directory schema", add, and click OK.
- Select "Attributes" in the list that appears, then find the attribute(s) you want to index.
- Right click on each one, and open their Properties.
- Select the "Index this attribute in Active Directory" checkbox, and press OK.

Reading from a Global Catalogue in a multi-domain forest
========================================================

If your Active Directory forest contains multiple domains, then the LDAP server on each domain controller will only offer a view of the users in that domain.

If you want to synchronize all accounts in the forest, you may access the Global Catalogue interface offered by selected Domain Controllers. This is a typical LDAP interface, on TCP port 3268, but is read-only. It is most useful to use this for your source, therefore.

To make an Active Directory Domain Controller a Global Catalogue:

- Open the *Active Directory Sites and Services* administration tool
- Find the Domain Controller you want in the Sites/Servers list, and right-click on it to open it's *Properties*
- In the window that appears, select the checkbox "Global Catalogue" and press OK

Specific attributes
===================

Active Directory contains some very useful specific attributes.

A useful list of how these attributes map to settings in the AD user interface is available at `http://portal.sivarajan.com/2010/07/aduc-and-ldap-reference-sheet.html <http://portal.sivarajan.com/2010/07/aduc-and-ldap-reference-sheet.html>`__.

LSC offers some functions to read and/or update some of these.

So far, we can:

* read userAccountControl (for example to check account status,...)
* update userAccountControl (for example, to enable/disable an account, force the user to change password on next login, set the password to never expire,...)
* read lastLogonTimestamp to find out the number of weeks since last successful login throughout the Active Directory domain
* set unicodePwd to create or replace the password

See details on how to do this :doc:`here <syncrulesforactivedirectory>`.

Password Synchronization
========================

Overview
--------

Synchronizing passwords with Active Directory is a common requirement, but generally a tricky subject. LSC does not solve this problem for you, but can help you achieve your goal. In particular:

* **LSC can write a password to Active Directory**, given the original password in clear text (there is no way to update a password in AD if you only have a hashed password, in MD5 or SHA, for example).

* **LSC cannot read passwords from Active Directory**.

Approaches
----------

There are several approaches to synchronizing passwords with Active Directory. A few we (the authors of LSC) have tried are metioned here. If you have other approaches, we'd love to hear about them.

- Store passwords in your source repository, either in clear text or in a two-way encryption scheme (LSC includes tools to encrypt and decrypt such an algorithm: see :doc:`here <syncrulessecurity>`, then use LSC's `AD tool-class <http://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#getUnicodePwd(java.lang.String)>`__ to update the password in AD. :doc:`here <syncrulesforactivedirectory>` an example of how to do this.
- Use AD as your main password store, and configure an OpenLDAP directory server to redirect authentications there, via SASL and LDAP binds. See `detailed documentation to set up OpenLDAP and saslauthd to bind to AD <https://www.ltb-project.org/documentation/sasl_delegation.html>`__.
- Configure Active Directory to capture password changes, by either:

   - Notifying another server on user password changes, using the `Password Synchronization <http://technet.microsoft.com/en-us/library/cc727636(WS.10).aspx>`__ component of Microsoft's **Services For Unix** addon.
   - Capturing the passwords, using `PasswdHk <http://passwdhk.sourceforge.net/>`__, a password filter DLL.

Pitfalls
--------

Active Directory is, er, a little peculiar in it's handling of password changes. Here are some hints, to avoid being bitten by it's weird behaviour, like we have been :-)

**Old password remains valid for an hour**

As of Windows Server 2003 SP1, once you've changed a user's password, the old password remains valid for an hour after the change. In effect, this means you can use both a user' old password and the users' new password to log in for one hour!

**New password accepted in LDAP modify operation but not really accepted**

In some cases (particularly with passwords containing special characters, such as non ASCII characters), Active Directory will accept a password update operation and return a "Success (0)" result for the LDAP modify operation, BUT the new password will not be useable.

For this reason, we recommend to always check that a succesful BIND operation can be performed on the Active Directory with the new password after changing it. You can use the :doc:`canBind* functions <syncrulesforldap>` to do this.

Non-standard object classes
===========================

Active Directory does not respect the **inetOrgPerson** objectClass definition, as specified in `RFC 2798 <http://tools.ietf.org/html/rfc2798>`__.  An explanation is provided below.

LSC version 2.X can synchronize to and from Active Directory despite of this.

The objectClass inheritance path defined in `RFC 2798 <http://tools.ietf.org/html/rfc2798>`__ is as follows:

  * top

    * person

      * organizationalPerson 

        * inetOrgPerson

However, in Active Directory, an extra objectClass, named **user** is inserted in this path:

  * top

    * person

      * organizationalPerson 

        * **user**

          * inetOrgPerson

This is documented by Microsoft in the `Active Directory Schema documentation <http://msdn.microsoft.com/en-us/library/ms682282(VS.85).aspx>`__.



.. rubric:: Footnotes

.. [#f1] See `SAM-Account-Type Attribute description <http://msdn.microsoft.com/en-us/library/ms679637(v=VS.85).aspx>`__.
