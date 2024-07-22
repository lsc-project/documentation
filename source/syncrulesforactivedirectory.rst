***************************************************
Extended synchronization rules for Active Directory
***************************************************


The LSC has some built-in functions to interact with some specific attributes of an Active Directory.

UserAccountControl (account type and status)
============================================

This attribute is a set of bits to manage a user in an AD. You can access it in the normal way in LSC (``dstBean.getAttributeValueById('userAccountControl')``).

Here are some methods to simplify changing values of this attribute.

userAccountControlSet
---------------------

You can set specific bits with the method `AD.userAccountControlSet <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#userAccountControlSet-int-java.lang.String:A->`__.

This method takes 2 parameters :

- The value (integer) of the userAccountControl
- An array of constants that will tell LSC to set (or unset) a specific bit

For example:

.. code-block:: js

    lsc>tasks>task>syncoptions>attribute(userAccountControl)>defaultValue = \
        AD.userAccountControlSet(
            dstBean.getDatasetFirstValueById('userAccountControl'),
            [AD.UAC_UNSET_ACCOUNTDISABLE])

You can find a list of all the constants in the *org.interldap.lsc.utils.directory.AD* class. The constants are prefixed by ``UAC_SET`` and ``UAC_UNSET``.

**Warning**: to create an entry in AD, you can set userAccountControl field but the flag ``UAC_PASSWD_NOTREQD`` is mandatory if no password is submitted. So you can have a ``create_value`` like this:

.. code-block:: js

    lsc>tasks>task>syncoptions>attribute(userAccountControl)>createValue =
        AD.userAccountControlSet( "0", [ AD.UAC_SET_PASSWD_NOTREQD,
            AD.UAC_SET_NORMAL_ACCOUNT ])

userAccountControlCheck
-----------------------

You can check if a specific bit is set with the method `AD.userAccountControlCheck <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#userAccountControlCheck-int-java.lang.String->`__.

This method takes 2 parameters:

- The value (integer) of the userAccountControl
- A constant that will tell LSC if a specific bit is set (or not)

For example :

.. code-block:: js

    AD.userAccountControlCheck(dstBean.getDatasetFirstValueById('userAccountControl'), \
        AD.UAC_ACCOUNTDISABLE)

You can find a list of all the constants in the *org.interldap.lsc.utils.directory.AD class*. The constants are prefixed by ``UAC_`` (without SET or UNSET after).

userAccountControlToggle
------------------------

You can toggle a specific bit with the method `AD.userAccountControlToggle <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#userAccountControlToggle-int-java.lang.String->`__.

This method takes 2 parameters :

- The value (integer) of the userAccountControll
- A constant that will tell LSC which bit to toggle

For example :

.. code-block:: js

    AD.userAccountControlToggle(dstBean.getDatasetFirstValueById('userAccountControl'), \
        AD.UAC_ACCOUNTDISABLE)

You can find a list of all the constants in the *org.interldap.lsc.utils.directory.AD class*. The constants are prefixed by ``UAC_`` (without SET or UNSET after).

Managing password
=================

LSC enables creating and changing passwords for users. Here are some methods to simplify changing values of this attribute.

getUnicodePwd
-------------

The `getUnicodePwd <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#getUnicodePwd-java.lang.String->`__ method encodes a string to fit the syntax of the unicodePwd attribute in AD, used to set the password. It requires that unicodePwd is declared as binary attribute in the LDAP connection settings.

So you can create a default password (e.g. "changeit") for created users by setting:

.. code-block:: js

    lsc>tasks>task>syncoptions>attribute(unicodePwd)>policy = KEEP
    lsc>tasks>task>syncoptions>attribute(unicodePwd)>createValue = AD.getUnicodePwd("changeit")



Warnings and various pitfalls
-----------------------------

Active Directory is, er, a little peculiar in it's handling of password changes. Checkout some :doc:`Active Directory synchronization hints <activedirectory>`, to avoid being bitten by it's weird behaviour, like we have been :-)

Last logon
==========

Active Directory stores the date and time of the last logon to a server, in different attributes:

* **lastLogonTimestamp**: This attribute is replicated across all domain controllers in a domain, as of Windows Server 2003.
*  **lastLogon**: This attribute is not replicated, it is local to each domain controller.

The format of these attributes is identical. It contains the timestamp of a user's last logon, with a variable precision. By default, this timestamp is only guaranteed to be updated every **2 weeks**, but this is configurable.

Beware when reading this value, and value that's less than 2 weeks old may just be due to the server imprecision.

getNumberOfWeeksSinceLastLogon
------------------------------

The `getNumberOfWeeksSinceLastLogon <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#getNumberOfWeeksSinceLastLogon-java.lang.String->`__ method takes the String value read from **lastLogonTimestamp** or **lastLogon**, and returns the number of weeks since the date recorded.

This can be useful to detect unused accounts. For example, delete any accounts that haven't been used for 3 months or more

.. code-block:: js

    lsc>tasks>task>MyTask>syncOptions>conditions>delete =
    AD.getNumberOfWeeksSinceLastLogon(srcBean.getDatasetFirstValueById("lastLogon")) > 12

Account expiry date
===================

To set the Active Directory formatted value for an account expiry attribute, you can use the `AD.getAccountExpires <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#getAccountExpires-java.lang.String->`__ methods.

Date manipulation
=================

Active Directory stores dates in number of 100-nanosecond ticks since 1 January 1601 00:00:00 UT. Unix usually stores dates as the number of seconds elapsed since the start of the epoch at 1 January 1970 00:00:00 UT (see `http://en.wikipedia.org/wiki/System_time <http://en.wikipedia.org/wiki/System_time>`__).

You can use the following methods to convert dates between UNIX and AD:

* `AD.aDTimeToUnixTimestamp <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#aDTimeToUnixTimestamp-java.lang.String->`__
* `AD.unixTimestampToADTime <https://lsc-project.org/javadoc/latest/org/lsc/utils/directory/AD.html#unixTimestampToADTime-java.lang.String->`__

