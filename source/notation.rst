********************
Notation conventions
********************

In order to simplify the notation, you will find the following notation in the documentation :

.. code-block:: js

    lsc>task>ldapSourceService>baseDn = ou=People,dc=lsc-project,dc=org

This is a shortcut for :

.. code-block:: XML

    <lsc>
      <task>
        <ldapSourceService>
          <baseDn>ou=People,dc=lsc-project,dc=org</baseDn>
        </ldapSourceService>
      </task>
    </lsc>

In this notation, all the nodes are not specified. This is just a way to precise that for the source service of the corresponding task, the base DN node must contain the "ou=People,dc=lsc-project,dc=org" value.

You may also notice the following notation :

.. code-block:: js

    lsc>tasks>task(ATask)>bean = org.lsc.beans.SimpleBean

This means that the task for which the bean value is described, is named "ATask" as shown below :


.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <name>ATask</name>
          <bean>org.lsc.beans.SimpleBean</bean>
        </task>
      </tasks>
    </lsc>

