***********************************************
Security-related extended synchronization rules
***********************************************

The class **SecurityUtils** offers 3 methods, available from all Javascript contexts.

Hash (MD5 or SHA1)
==================

To hash a value using MD5 or SHA1, you can call the `**SecurityUtils.hash** method <https://lsc-project.org/javadoc/latest/org/lsc/utils/SecurityUtils.html#hash-java.lang.String-java.lang.String->`__.

As an example, the following code will return the SHA1 hash of the string "phrase to hash":

.. code-block:: js

    SecurityUtils.hash(SecurityUtils.HASH_SHA1, "phrase to hash")

Symmetric Encryption
====================

Using a pre-generated key, you can encrypt or decrypt values using a two-way encryption algorithm.

General usage
-------------

All values passed to and returned from these fuctions are handled as base64 values, so they can be stored directly.

Example:

.. code-block:: js

    SecurityUtils.encrypt("something secret")
    SecurityUtils.decrypt(srcBean.getDatasetFirstValueById("encryptedAttribute"))

Configuration
-------------

Options are set through :doc:`Encryption options <encryption>`.

Creating a key
--------------

You can call a special method to create a key in the keypath configured above (or, by default, in ``lsc.key`` in the current configuration directory).

.. code-block:: console

    cd /path/to/your/lsc/installation
    LSC_SEC_CP=""; for jar in lib/*.jar; do LSC_SEC_CP="$LSC_SEC_CP:$jar"; done
    java -cp $LSC_SEC_CP org.lsc.utils.security.SymmetricEncryption -f $PWD/etc

Using an external script to encrypt password
--------------------------------------------

You can use **bin/passwordhk.pl** script to compute the password in the same way that LSC do.

.. note::

    Available in trunk or from version 2.0.3

    This script computes AES password, SSHA hash, search for a given user in a LDAP directory, and push the crypted password and the hash to this directory.

This case can be useful in conjunction with `password filter <https://passwdhk.sourceforge.io/>`__ hook (when synchronizing with Active Directory)


Microsoft / Samba LM/NT password hash
=====================================

This feature offers a simple way to encrypt a string so that it can be stored as a `LanMan compatible password <http://en.wikipedia.org/wiki/LM_hash>`__ or a `NT compatible password <http://en.wikipedia.org/wiki/NTLM>`__

Example:

.. code-block:: js

    SecurityUtils.computeSambaLMPassword("something secret")
    SecurityUtils.computeSambaNTPassword("something more secret")

Notes
-----

In some situations, it is necessary to install the "unrestricted policy files" for Java to be able to use 256 bit keys.

LSC expects the encoded password to be retrieved from LDAP base 64 encoded. 
So, if storing it using another tools, it must be base64 encoded before being inserted into LDAP.
