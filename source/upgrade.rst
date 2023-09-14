****************
Upgrade from 2.0
****************

Install the new version, trough packages or tarball. Here are some tips to adapt your current configuration to this new version.

XML namespace
=============

Update all configuration files to use the 2.1 XML namespace:

.. code-block:: XML

    <lsc xmlns="http://lsc-project.org/XSD/lsc-core-2.1.xsd" revision="0">

DN escaping
===========

We fixed some bugs about DN escaping. As you may know, characters like ``#``, ``,``, ``>``, ... must be escaped in a value used in the DN. To achieve this, you should now use the ``javax.naming.ldap.Rdn.escapeValue`` function to escape this kind of values. For example:

.. code-block:: XML

    <mainIdentifier>js:"cn=" + javax.naming.ldap.Rdn.escapeValue(srcBean.getDatasetFirstValueById("cn")) + ",OU=users,ou=demo,DC=example,DC=com"</mainIdentifier>

Dry run mode
============

Dry run mode allows to run the connector in read-only mode. In past versions, conditions were ignored in dry-run mode. They are now taken into account.

If you need to ignore a condition in dry-run mode, you need to use specific javascript variables in your code (see :doc:`Scripting <scripting>`).

YUM repository
==============

The URL of YUM repository has moved (see :doc:`Installation <installation>`).

Long task
=========

If you have tasks that need more than 1 hour to run, you may meet this kind of errors after the end of the task:

.. code-block::

    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=7C7CCF6C-ABC9-4B71-B 050-FF226F3DBBF3}: java.lang.NullPointerException
    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=A54C1F5A-150C-4DF7-9 C68-AFE33C8ABBDB}: java.lang.NullPointerException
    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=82851DC5-9CC3-42FF-8 4F6-C4FBA4CFFA22}: java.lang.NullPointerException
    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=DF3B36C8-34AB-4ED6-9 5EA-4F6E17D967D6}: java.lang.NullPointerException
    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=2AC6C771-2522-4862-9 B77-5FF202B8C2AA}: java.lang.NullPointerException
    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=86B2A7A2-E2E9-4B6F-9 DC0-16C4139D9D7E}: java.lang.NullPointerException
    Jun 14 21:03:03 - ERROR - Error while synchronizing ID {uid=B31710A6-26A4-40DD-B 330-EC7DB1746BBA}: java.lang.NullPointerException

This is because by default LSC end taks after 3600 seconds, and in 2.1 version, we now explicitly close connections at the end of the task.

You need to adjust the timeout to fit the duration of your task with the ``-i`` option in command line. For example:

.. code-block:: console

    lsc -f /etc/lsc/ -s user -t1 -i72000

