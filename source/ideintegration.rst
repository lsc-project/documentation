**********************************
Integrated Development Environment
**********************************

In order to develop your code in the project, you may want to integrate it directly in an IDE like `Eclipse <https://www.eclipse.org/>`__, `IDEA <https://www.jetbrains.com/idea/>`__ or `Netbeans <https://netbeans.apache.org/>`__. Please `contribute <https://lsc-project.org/contact.html>__` your own method if it is not present here :)

Eclipse
=======

Maven integration
-----------------

Ldap Synchronization Connector is built on top of Maven.

In old Eclipse releases, you had to install the Maven 2 plugin.

Now, most Eclipse IDE downloads already include support for the Maven build system through `M2E <https://eclipse.dev/m2e/documentation/m2e-documentation.html>`__. To check, use ``Help > About`` and check if you can see the Maven logo (M2E) sign.


Getting the source code
-----------------------

You have two options to obtain the LSC-sample source code:

- Download a release or a daily snapshot from the `download area <https://lsc-project.org/download.html>`__. Untar the archive in your workspace directory for example.
- Take the GIT version (if you want to check for new features,...):

    - We recommend using the integrated GIT plugin. Open the corresponding perspective in **Window** -> **Perspective** -> **Open Perspective** -> **Other**
    - Choose GIT
    - Right-click in the left pannel -> **Clone a git repository**. In **URI**, copy the LSC repository URI: ``https://github.com/lsc-project/lsc.git`` or ``git@github.com:lsc-project/lsc.git``
    - Clone the repository
    - Wait while Maven will downloads packages (can take time).
    - Attempt a build.

You should now have the project in Eclipse, with Maven dependencies integrated.


Running and debugging LSC
-------------------------

**Adapt your** ``etc/lsc.xml-sample``

accordingly to your desired settings, and rename it in ``lsc.xml``

**Launch LSC in Eclipse:**

Click on ``Run > Run Configurations``

Double-click on Java Application and set these options:

* Project: ``lsc-core``
* Main class: ``org.lsc.Launcher``

Then in the Arguments tab:

* Program arguments: ``-s all -f path-to-your-configuration-directory-or-file``
* For example with a directory: ``-s all -f /home/username/workspace_lsc/lsc/etc``
* Or with a configuration file: ``-s all -f /home/username/workspace_lsc/lsc/etc/lsc.xml``

In JRE tab:

Be sure to have a valid java version selected.

Just apply modifications and Run! (Ctrl+F11)


**Debug LSC**

Here is the magic of java: you can put breakpoints double-clicking at the lines you want the program to stop.

Once you have launched the program for the first time, the debug option should also have appeared: ``Run > Debug`` (F11)






Netbeans
========

These steps have been validated against a Simple Java NetBeans IDE 6.0.1. The Ldap Synchronization Connector is built with Maven, so you need to install the plugin:

* In the **Tools** menu, choose the **Plugins** item.
* Choose the **Available plugins (..)** tab and find the **Maven** line. Tick it on the left.
* Click on the **Install** button below.
* A new installation wizard will be opened. Choose **Next**, accept the license and click on **Install**.
* After ending the plugin installation, restart your IDE.
* Then return to the **Tools** menu and choose **Options** item.
* Click on the **Miscellaneous** tab and next on the **Maven2** tab.
* On the Maven2 home line, click on **Browse** button and find the home path of your local Maven2 installation.
* On the local repository line, click on **Browse** button and find the home path of your local Maven2 repository (currently ``~/.m2/repository``).
* Click on the **Index Now** button.

You are now ready to open your project. Just go in the **File** menu and choose **Open Project** item. Go to the unzipped directory ``lsc-sample``. The IDE will normally find the needed stuff and will include it ready for use.

IDEA
====

Please feel free to contribute the way to do it or the license :)

