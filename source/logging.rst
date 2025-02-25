**************************************
Managing output format through LogBack
**************************************

LSC uses the LogBack library to output various messages. You can enable multiple outputs with different format by simply changing the configuration in the ``/etc/lsc/logback.xml`` file.

Create an appender
------------------

The outputs uses the layouts of logback. So you first have to create an appender, you can find documentation of the appenders here : `http://logback.qos.ch/manual/appenders.html <http://logback.qos.ch/manual/appenders.html>`__.

The configuration for a file appender is here : `http://logback.qos.ch/manual/appenders.html#FileAppender <http://logback.qos.ch/manual/appenders.html#FileAppender>`__.

Then you have to include this Appender (which is only the output destination description) to a logger like the following sample :


.. code-block:: XML

    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <.../>
    </appender>
    <logger name="org.lsc" level="INFO">
         <appender-ref ref="FILE"/>
    </logger>

Output the synchronization to CSV files
---------------------------------------

In LSC you have a specific layout to log every action in a CSV file.

This uses the logback logs, so you have to declare this in the ``/etc/lsc/logback.xml`` file.

Here is a small example : 

.. code-block:: XML

    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <File>/var/log/lsc/output.csv</File>
        <Append>true</Append>
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.lsc.utils.output.CsvLayout">
                <logOperations>create,update</logOperations>
                <attrs>dn;uid;sn;givenName;description;cn;mail;objectClass;userAccountControl</attrs>
                <separator>;</separator>
                <outputHeader>true</outputHeader>
                <taskNames>MyTask</taskNames>
            </layout>
        </encoder>
    </appender>

Use the ``ch.qos.logback.core.FileAppender`` or any subclass as an appender.

Specify the path to the csv file you want to write to in the tag ``<File>``.

Use the ``org.lsc.utils.output.CsvLayout`` for the layout

This specific layout has 5 options :

- **logOperations**: which actions you want to log (create, update, delete, modrdn). You can specify multiple actions by grouping them with commas ",".
- **separator**: which separator you want to use in the output (default a semi-colon ";")
- **attrs**: which attributes you want to write. The attributes MUST be separated by the property **separator**. You can specifiy empty columns if you want to. Note that only the first value is displayed in case of multi-valued attributes.
- **outputHeader**: true or false. This will print the "attrs" option before outputting any data lines - thus your CSV file can have a standard header.
- **taskNames**: a coma separated list of tasks you want to output in CSV. If the option is absent or if its value is empty, all tasks will be logged.

.. note::

    The special attribute name ``dn`` can be used to output the entry's distinguishedName.

You can specify multiple loggers using the same layout and different options. For example, you can specify 4 loggers, one for each actions.

Output the synchronization to LDIF files
----------------------------------------

In LSC you have a specific layout to log every action in a LDIF file.

This uses the logback logs, so you have to declare this in the ``/etc/lsc/logback.xml`` file.

Here is a small example:

.. code-block:: XML

    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <File>/var/log/lsc/output.ldif</File>
        <Append>true</Append>
        <encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
            <layout class="org.lsc.utils.output.LdifLayout">
                <logOperations>create,update</logOperations>
                <onlyLdif>true</onlyLdif>
                <Pattern>%date{MMM dd HH:mm:ss} - %-5level - %message%n</Pattern>
            </layout>
        </encoder>
    </appender>

The ``LdifLayout`` has 2 custom options:

- **logOperations**: which actions you want to log (create, update, delete, modrdn). You can specify multiple actions by grouping them with commas ",".
- **onlyLdif**: if ``true``, only the ldif operations will be logged (+ a comment prefixed by #). If ``false``, other events could be logged. (see below)

If **onlyLdif** is set to ``true``, ``LdifLayout`` would inherit from standard ``PatternLayout`` for logging events other than ldif. In this situation, you should add any other option from ``PatternLayout``, but take care to set at least the following property:

- **Pattern**: The pattern for formatting the log event. See `PatternLayout documentation <https://logback.qos.ch/manual/layouts.html#ClassicPatternLayout>`__ for more information. Also pay attention to use the correct logger name and logger level for catching the appropriate events.

Embedded OpenDJ logging
=======================

More information can be found at the following location `https://docs.oracle.com/cd/E19476-01/821-0506/accessing-logs.html <https://docs.oracle.com/cd/E19476-01/821-0506/accessing-logs.html>`__

Base installation is in ``/tmp/opends-test``

