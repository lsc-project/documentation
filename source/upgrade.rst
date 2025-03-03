****************
Upgrade from 2.1
****************

Install the new version, through packages or tarball. Here are some tips to adapt your current configuration to this new version.


Installation and requirements
=============================

As described in :doc:`installation <installation>` and :doc:`requirements <requirements>` sections, there are somes changes:

* LSC 2.2 is bundled only for RHEL-like systems 8 and 9, and debian-like systems 11 and 12
* LSC 2.2 packages now require java 21 for running. However, it still compatible with java 11 and 17 if you build the code yourself.
* the GPG key signing the packages has changed to ``RPM-GPG-KEY-LTB-PROJECT-SECURITY``. You must import this new key.


XML namespace
=============

Update all configuration files to use the 2.2 XML namespace:

.. code-block:: XML

    <lsc xmlns="http://lsc-project.org/XSD/lsc-core-2.2.xsd" revision="0">

Google Apps Destination service
===============================

This service is obsolete and has been removed in 2.2 version.

Audits
======

Audit configuration has been removed in 2.2.

If you have some configuration sections containing:

.. code-block:: XML

    <audits>
      <audit>
        ...
      </audit>
    </audits>

You must remove them and use :doc:`logback configuration <logging>` instead.

Behavior change for multi-valued attribute replacement
======================================================

Before version 2.2, during an entry update, LSC behavior was to systematically replace all values for attributes in FORCE policy.

This could lead to slow modifications, especially for entries with many values,
like groups holding many members. However, sometimes it's cheaper
to only add the missing values plus remove the extra values.

LSC 2.2 takes this problematic into consideration.

If (number of missing values + number of extra values) < number of expected values after modification, then LSC applies 2 operations:

* 1 add of missing values
* 1 delete of extra values

else, it performs a full replace with all values.
