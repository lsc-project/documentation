********************************************
Handling sequences for synchronization rules
********************************************

The class **SequencesFactory** offers 2 main methods, available from all Javascript contexts.

Sequences are handled by updatable entries inside the directory, thus getting a new value will increment the attribute that is used as a counter.

Java object is documented there: `SequencesFactory <http://lsc-project.org/javadoc/latest/org/lsc/utils/SequencesFactory.html>`__

Reading the current value
=========================

To read the current sequence value, just call the getCurrentValue(DN, Attribute) method.

If an error is encountered, the call will return the -1 value.

Getting the next value
======================

To get the next value, just call the getNextValue(DN, Attribute) method on the sequence. 

If an error is encountered, the call will return the -1 value.

But the next call to the same method will return an incremented value because each time the method is called, the directory value will be updated!

Sample
======

Let just imagine that you need to handle the uidNumber value inside a directory.

First create somewhere inside your directory the following entry:

.. code-block::

    dn: cn=uidNumberSequence,ou=Sequences,ou=HiddenTree,dc=lsc-project,dc=org
    cn: uidNumberSequence
    objectClass: device
    objectClass: top
    serialNumber: 1024

Then inside the task settings, inside the propertiesBasedSyncOptions node, add the following XML elements to specify the previous entry:

.. code-block:: XML

    <dataset>
        <name>uidNumber</name>
        <policy>KEEP</policy>
        <defaultValues>
            <string>
                SequencesFactory.getInstance(ldap.getJndiServices()).
                getNextValue("cn=uidNumberSequence,ou=Sequences,ou=HiddenTree,dc=lsc-project,dc=org",
                             "serialNumber")
            </string>
        </defaultValues>
    </dataset>

Take care to the following items:

  * always use a KEEP policy, because otherwise the counter will increment each time an object is synchronized even if the object is not updated !
  * you can either specify that the sequence will be looked up inside the source or the destination directory respectively with on the ``srcLdap.getJndiServices()`` or ``ldap.getJndiServices()``

