*******
Run LSC
*******

.. note::

    See :doc:`basics <basics>` to get more details on how LSC works

Starting LSC
============

LSC can be started with either synchronous or asynchronous mode. In synchronous mode, LSC can also schedule a clean mode at the end of the synchronous mode.

You can launch all tasks by using the keyword ``all``, but they will not be launched in the order of their definition in the XML file. To launch ``task1`` and then ``task2``, separate them with a comma.

Synchronous launch
==================

.. code-block:: console

    lsc -s all

.. code-block:: console

    lsc -s task1,task2


Clean launch
============

.. code-block:: console

    lsc -c all

.. code-block:: console

    lsc -c task1,task2

.. note::

    Clean mode can be combined with synchronous mode but not with asynchronous mode.

Asynchronous launch
===================

.. code-block:: console

    lsc -a all

.. code-block:: console

    lsc -a task1,task2

When multiple asynchronous tasks are started, they are started as separate thread inside the same daemon.

Using systemd service
=====================

Some systemd services are provided by LSC, installed by default with rpm/deb packages.

This script allows to launch LSC in asynchronous mode:

.. code-block:: console

    systemctl start lsc-async

or in synchronous mode:

.. code-block:: console

    systemctl start lsc-sync

You can get the process status:

.. code-block:: console

    systemctl status lsc-sync

asynchronous tasks can be customized in ``/etc/default/lsc-async``:

.. code-block::

    #====================================================================
    # Configuration for LSC init script
    # (http://www.lsc-project.org).
    #====================================================================
    
    # JMX
    LSC_JMXPORT="1099"
    
    # JAVA
    #JAVA_HOME=/usr/java/jdk/jre
    
    LSC_CONFIG_DIR="/etc/lsc"
    #LSC_SYNC_TASKS=""
    LSC_ASYNC_TASKS="-a all"
    #LSC_CLEAN_TASKS=""
    LSC_EXTRA_ARGS=""

synchronous tasks can be customized in ``/etc/default/lsc-sync``:

.. code-block::

    #====================================================================
    # Configuration for LSC init script
    # (http://www.lsc-project.org).
    #====================================================================
    
    # JMX
    LSC_JMXPORT="1099"
    
    # JAVA
    #JAVA_HOME=/usr/java/jdk/jre
    
    LSC_CONFIG_DIR="/etc/lsc"
    LSC_SYNC_TASKS="-s all"
    #LSC_ASYNC_TASKS=""
    LSC_CLEAN_TASKS="-c all"
    LSC_EXTRA_ARGS=""


Just uncomment the section you want to customize.

You can also easily run several connectors with systemd instances.

Customize the corresponding configuration file:

.. code-block:: console

    # For a synchronous task
    cp /etc/default/lsc-sync /etc/default/lsc-examplesynctask
    
    # For an asynchronous task
    cp /etc/default/lsc-async /etc/default/lsc-exampleasynctask

Edit ``/etc/default/lsc-examplesynctask`` or ``/etc/default/lsc-exampleasynctask``

Run the corresponding systemd instance:

.. code-block:: console

    # synchronous systemd instance will load file /etc/default/lsc-<my-task>
    systemctl start lsc-sync@examplesynctask
    
    # asynchronous systemd instance will load file /etc/default/lsc-<my-task>
    systemctl start lsc-async@exampleasynctask

Interaction with an already started instance
============================================

When LSC is started in asynchronous mode, it is still possible to request operations through the JMX protocol.

LSC daemon required options for JMX remote invocation
-----------------------------------------------------

You will have to launch the LSC instance with at least one asynchronous task and the JMX port specified:

.. code-block:: console

    export LSC_JMXPORT=1099
    lsc -a <comma separated list of tasks|"all" keyword>


.. warning::

    But doing such settings, please notice that anyone can send commands to your LSC instance listening on all your network interfaces on the 1099 TCP port ! (At this time this is not a simple task to bind JMX to the local interface only, RFU :)

LSC remote invocation by lsc-agent command line tool
----------------------------------------------------

The lsc-agent command line tool is able to connect remotely to the LSC instance and to do some interesting things :
  * **-a** gives the ability to start a named task
  * **-l** provides a list of asynchronous tasks
  * **-s** gives the status (running or not) of an named task

When starting a task, if the id and pivot attributes are provided (**-i** and **-t** options), LSC will start the corresponding synchronous task. If not, LSC will start the corresponding asynchronous task.

Of course it is possible to specify an hostname and a port to connect to another host through **-h** and **-p** parameters.
