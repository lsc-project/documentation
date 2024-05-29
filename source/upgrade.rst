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
