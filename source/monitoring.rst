**********
Monitoring
**********

LSC provides scripts to check status in Nagios.

Check LSC status file
=====================

This script parses LSC logfile to find a status line, and extract the following data:

* Total of entries
* Entries do modify
* Modified entries
* Entries in error

The best practice to use this script is to have a status file configured in logback.xml:

.. code-block:: XML

    <appender name="LSC_STATUS" class="ch.qos.logback.core.FileAppender">
            <append>false</append>
            <File>/tmp/lsc/log/lsc.status</File>

            <layout class="org.lsc.utils.output.LdifLayout">
                    <Pattern>%date{MMM dd HH:mm:ss} - %-5level - %message%n</Pattern>
            </layout>

            <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
                    <level>INFO</level>
            </filter>
    </appender>

This file is erased at each launch, so you are sure to get the latest status.

You can then use the monitoring script:

.. code-block:: console

    ./check_lsc_status_file.pl -w 1 -c 1 -l /tmp/lsc/log/lsc.status -f -d 3600

The options are:

* ``w``: warning level
* ``c``: critical level
* ``l``: log file
* ``d``: log file max age in seconds
* ``f``: perfparse data

Check LSC remote
================

When LSC is launched as an asynchronous task, you cannot get the status through log file.

Instead, you can use LSC agent script to get the status. A Nagios script doing this is provided and extract the following data:

* Total of entries
* Entries do modify
* Modified entries
* Entries in error

You can  use the monitoring script like this:

.. code-block:: console

    ./check_lsc_remote.pl -H localhost -p 1099 -w 1 -c 1 -t user -f

The options are:

* ``w``: warning level
* ``c``: critical level
* ``H``: JMX host
* ``p``: JMX port
* ``f``: perfparse data
* ``t``: task name
* ``b``: path to lsc-agent script
