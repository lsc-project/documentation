**********************************************
Apply modifications on an LDAP entry on delete
**********************************************

Presentation
============

A typical use case is to desactivate an LDAP entry instead of deleting it, 
if the source data does not contain this entry but the destination data contains it.

The **cleanup** phase is used to deal with thise use case, but it ends with deleted
entries from the source to be deleted from the destination.

Here we will modify an attribute, and move the entry to a specific branch, instead of deleting it.

We will show how we can create and manipulate **Java** classes in order to fulfill this goal

Defining the modifications to apply
===================================

First let's list what we want to do:

* We will modify the ``mailboxAccountStatus`` attribute from the entry. By default, this attribute's value is set to ``active`` (we assume the attribute exists and is declared in the schema)
* We will move the modified entry to a specific branch named ``uid=blah,cn=disabled,...`` (here, we assume that the entry is identified by the ``uid`` attribute)

The source LDAP server contains the following entries:

* ``uid=00000004,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000005,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000007,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000009,cn=mailboxes,ou=fr,o=service,o=acme``

and the target LDAP server contains the following entries:

* ``ou=disabled,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000003,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000004,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000005,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000006,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000007,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000008,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000009,cn=mailboxes,ou=fr,o=service,o=acme``

Here we will change the following entries:

* ``uid=00000003,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000006,cn=mailboxes,ou=fr,o=service,o=acme``
* ``uid=00000008,cn=mailboxes,ou=fr,o=service,o=acme``

as they don't exist in the source LDAP server, but are present in the destination LDAP server.

Those entries contains one attribute, ``mailboxAccountStatus`` which is set to ``active`` and that is going to be changed to ``disabled``, 
then moved to the ``ou=disabled`` branch we can see in the previous picture. 

Here is the content of the original entry:

.. code-block::

    dn: uid=00000003,cn=mailboxes,ou=fr,o=service,o=acme
    objectClass: inetOrgPerson
    objectClass: mailbox
    objectClass: uidObject
    cn: cn00000003
    sn: sn00000003
    uid: 00000003
    displayName: displayName00000003
    givenName: givenName00000003
    mailboxAccountStatus: active
   ...

Adapt LSC configuration file
============================

We now have to modify the **LSC** configuration file to get this done.

We will modify the ``<task>/<propertiesBasedSyncOptions>/<conditions>`` parts of the file, and more specifically the ``<delete>`` condition.

Keep in mind that this condition will only be applied in **cleanup** phase.

Modifying an attribute can only be done using some specific **Java** classes we don't have access to in the **JavaScript** code.
We will need to get access to those classes.

Here is the list of classes we need to getaccess to:

* ``javax.naming.directory.BasicAttribute``
* ``javax.naming.directory.ModificationItem``
* ``javax.naming.directory.DirContext``
* ``java.util.ArrayList``
* ``org.lsc.jndi.JndiModifications``
* ``org.lsc.jndi.JndiModificationType``

With the **GraalVM** **Javascript** engine, the way to access such classes and their fields and members is to use the `̀`Java.type(<class>)`` function.

For instance, to create a list, we simply write:

.. code-block:: javascript
    
    var arrayListClass = Java.type('java.util.ArrayList');
    var list = new arrayListClass();
    list.add("value");

As we can see, we first have declared the class, then created an instance, and call one of its method.

For static fields, it's simpler:

.. code-block:: javascript
    
    var replaceType = Java.type('javax.naming.directory.DirContext').REPLACE_ATTRIBUTE

Here, we just get the ``REPLACE_ATTRIBUTE`` static field directly without having to create an instance.

Note that when calling the constructor, you can pass some arguments.

Finally, here is the place where we are going to inject our script:

.. code-block::

    <task>
      <propertiesBasedSyncOptions>

        <mainIdentifier>srcBean.getMainIdentifier()</mainIdentifier>
        <defaultDelimiter></defaultDelimiter>
        <defaultPolicy>FORCE</defaultPolicy>

        <conditions>
          <create><![CDATA[...]]></create>
          <update><![CDATA[...]]></update>
          <delete><![CDATA[
              --> Here <--
            ]]
          </delete>

Modifying the entry
===================

The first section of the code will modify the entry, and more specifically replace an attribute's value:

.. code-block:: javascript

        // Get the original entry's DN
        var dn = dstBean.getDN();
        
        //-------------------------------------------------
        // First modify the mailboxAccountStatus attribute
        //-------------------------------------------------
        // We create the Attribute and its value
        var attributeClass = Java.type('javax.naming.directory.BasicAttribute');
        var modifiedAtribute = new attributeClass("mailboxAccountStatus");
        modifiedAtribute.add("disabled");
        
        // We create the type of modification we want to apply (REPLACE)
        var modificationItemClass = Java.type('javax.naming.directory.ModificationItem');
        var modificationItem = new modificationItemClass(
            Java.type('javax.naming.directory.DirContext').REPLACE_ATTRIBUTE,
            modifiedAtribute);
        
        // We on ly have one modification but the API requires
        // we pass a LIST, let's create it, and add the modification
        // to this list
        var arrayListClass = Java.type('java.util.ArrayList');
        var modificationItems = new arrayListClass();
        modificationItems.add(modificationItem);
        
        // Now create the modifications that will be applied
        // It will be a MODIFY_ENTRY
        var jndiModificationsClass = Java.type('org.lsc.jndi.JndiModifications');
        var jndiModifications = new jndiModificationsClass(
            Java.type('org.lsc.jndi.JndiModificationType').MODIFY_ENTRY);
        jndiModifications.setDistinguishName(dn);

        // Store the modification's list into it
        jndiModifications.setModificationItems(modificationItems);

        // Get the LDAP service we will use to apply the modifications
        var services = ldap.getJndiServices();
                    
        // Then apply the entry's modification
        services.apply(jndiModifications);


Moving the entry
================

Here is the code that moves the entry in a second step:

.. code-block:: javascript
    
            //------------------------------------
            // Moving the entry into a 'disabled branch
            //------------------------------------
            // Get the entry's RDN
            var uid = dstBean.getDatasetFirstValueById("uid");
            
            // Define the new DN
            var newDn = "uid=" + uid + ",ou=disabled,cn=mailboxes,ou=fr,o=service,o=acme"; 
            
            // Create a MODRN modification
            var jndiModifications = new jndiModificationsClass(
                Java.type('org.lsc.jndi.JndiModificationType').MODRDN_ENTRY);
            jndiModifications.setDistinguishName(dn)
            jndiModifications.setNewDistinguishName(newDn);
            
            // And apply the modification
            services.apply(jndiModifications);
            
            // Last,  not least, return 'false' so that the entry is not deleted
            false

The two blocks of code must be added one after the other in the ``<delete>`` condition.

As we can see, we have two calls to the ``services.apply(jndiModifications)`` method, because we can't apply a modification and a move in a single operation.

That's it, when executed this code should replace the attribute and move the entry as expected!

All in all, this is pretty much like coding in Java, with some extra steps.
