.. LSC documentation master file, created by
   sphinx-quickstart on Mon Sep 11 17:23:40 2023.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to LSC's documentation!
===============================

This page lists all available documentation around LSC. If you don't find what you're looking for, check out the `Community Resources <https://lsc-project.org/contact.html>`__ page to find some help.

If you find any errors or you think something's missing here, please `let us know <https://lsc-project.org/contact.html>`__! We do our best to keep docs up to date, but hey, mistakes are human and things change quickly :)


.. toctree::
   :maxdepth: 1
   :caption: Working principles

   about
   basics


.. toctree::
   :maxdepth: 1
   :caption: Installation

   requirements
   installation
   upgrade

.. _index-configuration:

.. include::   index_configuration.rst

.. toctree::
   :maxdepth: 1
   :caption: Configuration
   :hidden:
   
   index_configuration
   notation
   overview
   connections
   connection_ldap
   connection_database
   tasks
   services
   service_source_database
   service_source_ldap
   service_source_asyncldap
   service_destination_database
   service_destination_ldap
   syncrules
   syncrulesforldap
   syncrulesforactivedirectory
   syncrulessecurity
   sequences
   encryption
   database
   persistence
   logging
   scripting


.. toctree::
   :maxdepth: 1
   :caption: Usage

   quickstart
   run
   plugins
   monitoring


.. toctree::
   :maxdepth: 1
   :caption: Howtos and tutorials

   activedirectory
   ssltls
   hsqldb
   kerberos
   openldaptoactivedirectory
   csvtoldap
   synchronizegroups
   synchronizeposixgroupstogroupofnames
   convertrecursivegroupstoflatgroups
   usecustomjavapackage
   applymodificationondelete


.. _index-development:

.. toctree::
   :maxdepth: 1
   :caption: Development

   development
   ideintegration
   codestyle
   contributing
   release
   packaging
   plugin-dev
   embedding
   third-parties


Contacts
-------------

* `Community resources <https://lsc-project.org/contact.html>`__
* `Professional services <https://lsc-project.org/professionalservices.html>`__

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
