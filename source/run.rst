*******
Run LSC
*******

.. note::

    See `basics <basics>`__ to get more details on how LSC works

Starting LSC
============

LSC can be started with either synchronous or asynchronous mode. In synchronous mode, LSC can also schedule a clean mode at the end of the synchronous mode.

You can launch all tasks by using the keyword ``all``, but they will not be launched in the order of their definition in the XML file. To launch ``task1`` and then ``task2``, separate them with a comma.

Synchronous launch
==================

.. code-block:: bash

    lsc -s all

.. code-block:: bash

    lsc -s task1,task2


Clean launch
============

.. code-block:: bash

    lsc -c all

.. code-block:: bash

    lsc -c task1,task2

.. note::

    Clean mode can be combined with synchronous mode but not with asynchronous mode.

Asynchronous launch
===================

.. code-block:: bash

    lsc -a all

.. code-block:: bash

    lsc -a task1,task2

When multiple asynchronous tasks are started, they are started as separate thread inside the same daemon.

Using start/stop service script
===============================

A service script is provided in LSC, installed by default with RPM. This script allows to launch LSC in asynchronous mode:

.. code-block:: bash

    /etc/init.d/lsc start

You can test configuration:

.. code-block:: bash

    /etc/init.d/lsc configtest

And get the process status:

.. code-block:: bash

    /etc/init.d/lsc status

This service script can be configured in ``/etc/default/lsc``:


.. code-block::

    #====================================================================
    # Configuration for LSC init script
    # (http://www.lsc-project.org).
    #====================================================================

    # LSC installation
    LSC_BIN="/usr/local/lsc/bin/lsc"
    LSC_CFG_DIR="/usr/local/lsc/etc"
    LSC_USER=""
    LSC_GROUP=""
    LSC_PID_FILE="/var/run/lsc.pid"
    LSC_TASKS="all"
 
    # JMX
    LSC_JMXPORT="1099"
 
    # JAVA
    #JAVA_HOME=/usr/java/jdk/jre



This allows you to run several connectors. You just have to duplicate the service to run it:

.. code-block:: bash

    ln -s /etc/init.d/lsc /etc/init.d/lsc2
    cp /etc/default/lsc /etc/default/lsc2


Interaction with an already started instance
============================================

When LSC is started in asynchronous mode, it is still possible to request operations through the JMX protocol.

LSC daemon required options for JMX remote invocation
-----------------------------------------------------

You will have to launch the LSC instance with at least one asynchronous task and the JMX port specified:

.. code-block:: bash

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
