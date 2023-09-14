.. LSC documentation master file, created by
   sphinx-quickstart on Mon Sep 11 17:23:40 2023.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to LSC's documentation!
===============================

This page lists all available documentation around LSC. If you don't find what you're looking for, check out the :doc:`Community Resources <community-resources>` page to find some help.

If you find any errors or you think something's missing here, please :doc:`let us know <community-resources>`! We do our best to keep docs up to date, but hey, mistakes are human and things change quickly :)


.. toctree::
   :maxdepth: 1
   :caption: Working principles

   basics


..
   TODO: remove download page (https://lsc-project.org/doku.php/download) from doc. It will appear only on lsc-website: https://github.com/lsc-project/lsc-website/

.. toctree::
   :maxdepth: 1
   :caption: Installation

   requirements
   installation
   upgrade

.. include::   index_configuration.rst

.. toctree::
   :maxdepth: 1
   :caption: Configuration
   :hidden:
   
   index_configuration
   notation
   overview
   connections
   connections_ldap
   connections_database
   connections_googleapps
   tasks
   services
   service_source_database
   service_source_ldap
   service_source_asyncldap
   service_destination_database
   service_destination_ldap
   service_destination_googleapps
   syncrules
   syncrulesforldap
   syncrulesforactivedirectory
   syncrulessecurity
   sequences
   audits
   encryption
   database
   persistence
   logging
   scripting


.. toctree::
   :maxdepth: 1
   :caption: Usage

   run
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


.. toctree::
   :maxdepth: 1
   :caption: Plugins

   nis
   obm
   executable

Non official plugins:

   * `Multi JDBC source <https://github.com/MSSputnik/lsc-multi-jdbc-src-service>`__

.. toctree::
   :maxdepth: 1
   :caption: Development

   development
   packaging
   javadoc
   plugin
   embedding


..
   TODO: contact pages are to be moved to lsc-website: https://github.com/lsc-project/lsc-website/

.. toctree::
   :maxdepth: 1
   :caption: Contacts

   community-resources
   professionalservices

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
