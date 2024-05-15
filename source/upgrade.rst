****************
Upgrade from 2.1
****************

Install the new version, through packages or tarball. Here are some tips to adapt your current configuration to this new version.

XML namespace
=============

Update all configuration files to use the 2.2 XML namespace:

.. code-block:: XML

    <lsc xmlns="http://lsc-project.org/XSD/lsc-core-2.2.xsd" revision="0">

Google Apps Destination service
===============================

This service is obsolete and has been removed in 2.2 version.


Behavior change for multi-valued attribute replacement
======================================================

Before version 2.2, during an entry update, LSC behavior was to systematically replace all values for attributes in FORCE policy.

This could lead to slow modifications, especially for entries with many values,
like groups holding many members. However, sometimes it's cheaper
to only add the missing values plus remove the extra values.

LSC 2.2 take this problematic into consideration.

If (number of missing values + number of extra values) < number of expected values after modification, then LSC applies 2 operations:

* 1 add of missing values
* 1 delete of extra values

else, it performs a full replace with all values.
