*********
About LSC
*********

What is LSC?
============

**Ldap Synchronization Connector** synchronize data from any data source including **databases, LDAP directories or files** by reading, transforming and comparing these data between the source and the target referentials. These connectors can then be used to continuously synchronize a data source to a directory, for a one shot import or just to compare differences by outputting CSV or LDIF format reports.

LSC offers a **powerful transformation engine**, based on a scripting language, to easily manipulate data on the fly.

Various **identity management functions** are included for directory-specific compatibility - most notably Active Directory (changing passwords, account status, last logon,...).

LSC is an **open source** project written in Java, available under the `BSD license <http://www.opensource.org/licenses/bsd-license.php>`__.

Philosophy
==========

**Make it possible**: The main goal is to provide a simple and efficient way of synchronizing any data source to a LDAP directory quickly.

**Make it stable and safe**: Many companies use home-grown scripts for this kind of synchronization. LSC is an open source tool, and all the bugs you might write in your scripts have been fixed already (and a few more). The code is in use worldwide and is heavily tested. LSC is simply about not reinventing the wheel to synchronize data.

**Make it faster and simpler**: The added-value of LSC resides in the focus on identity management tools - common transformations and directory-specific behaviour come as part of the software. These functions are extensible to include your own, to integrate into your existing infrastructure. Save time, by reusing!

Feature overview
================

* **Multiple source connectors**: any LDAPv3 server, any database with a JDBC connector, flat files, third parties like Google Apps and anything else you write a connector for
* Support for LDAPv3 niceties and extensions: **StartTLS**, **LDAPS**, **paged results**, LDAP Sync / Persistent Search, schema retrieval
* Graphical installation tool (use is optional)
* Fully :doc:`configurable <overview>` through a **XML configuration file**
* Written in Java, leveraging the ecosystem of available tools
* Simple wrapper shell scripts are provided, to ease use and system integration
* Runs on any Java8-enabled platform - tested on Windows, Linux and MacOS X
* Simple **attribute mapping** from source to destination
* Three policies to update attributes, including forcing values, non-destructive updates and merging
* **Advanced attribute manipulation** via a built-in **JavaScript** and Groovy engine
* Predefined libraries for use in JavaScript attribute manipulation:

    * LDAP server tools: standard bind operation can be checked on any LDAPv3 server
    * **Active Directory** tools: password update (unicodePwd attribute), account type and status manipulation (userAccountControl), unused account detection (lastLogonTimestamp)
    * String manipulation: formatting for common tasks in identity management, such as capitalizing first letters in a complex name, filtering accents for login names,...
    * Security tools: password hashing, bi-directional encryption

* Conditions to only create, update, rename or delete entries depending on current values
* Detailed and configurable **logging** in LDIF (fully RFC-compliant) and CSV formats
* Monitoring plugin for Nagios
* Possibility to extend any Java class to implement your own specific synchronization needs

Identity synchronization
========================

The basic principles LSC was created for, with regard to synchronizing identities are as follows.

Types of identity information
-----------------------------

Two levels of identity information can be distinguished:

- A person's identity: this is the existence of an individual, frequently assimilated to an **account** (equivalent to an object in a LDAP directory).
- The information defining a person's identity: these are the various details concerning one person, for example their **name**, **address** and **phone numbers** (equivalent to attributes within an object in a LDAP directory).

A unique identifier
-------------------

Matching source and destination entries requires a common piece of information. In LSC we call this the **pivot**.

The pivot can be one or several attributes, that form a unique combinaison (similar to a primary key in a database). Commonly used pivots are UIDs and email adresses.

Identity synchronization operations
-----------------------------------

When reconciliating two identity repositories, three operations are possible for each individual identity:

- **Create**: If the identity exists in only one repository, it may be created in the other. In this case all information within the identity originates from the repository containing the identity, or from default values specified on a general basis.
- **Update**: If the identity exists in both repositories, the information within must be updated, thus synchronizing each element from one repository towards the other. Different repositories may have precedence over different elements of information.
- **Delete**: If the identity exists in only one repository, it may be deleted in the other. In this case, the identity and all information within is completely removed.

Contact
=======

Don't hesitate to contact the community `here <https://lsc-project.org/contact.html>`__

You can also get professional support `here <https://lsc-project.org/professionalservices.html>`__

Contributors
============

Find out `who's behind the project <https://lsc-project.org/professionalservices.html>`__


