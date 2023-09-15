***********************
Database source service
***********************

The Database source service allows to connect to any JDBC compliant database and to look for data that will be synchronized.

The Database source service is using a :doc:`database connection <connection_database>` to get the various connection settings. The requests are served by :doc:`iBatis <database>` ORM engine.

Let's find below a sample and the parameters description:

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <databaseSourceService>
            <name>MySyncTask-src</name>
            <connection reference="jdbc-src-conn"/>
            <requestNameForList>getInetOrgPersonList</requestNameForList>
            <requestNameForObject>getInetOrgPerson</requestNameForObject>
            <requestNameForNextId>getNextInetOrgPersonId</requestNameForNextId>
            <requestNameForClean>getInetOrgPersonClean</requestNameForClean>
          </databaseSourceService>
        </task>
      </tasks>
    </lsc>

The Database source service should be configured by using the following settings:

* **name**: mandatory, it should contain any value that will uniquely identify this service regarding a task
* **connection**: this node should not contain any element, just a ``reference`` attribute which is used to look for the corresponding connection with this name (as sub-element of the connection element)
* **requestNameForList**: node providing the SQL request name to list available objects in the data source (used through iBatis)
* **requestNameForObject**: node providing the SQL request name to get the full object according to the datasets identifying it uniquely
* **requestNameForNextId**: this node may be provided to enable an asynchronous mode of the datasource. If provided, this request will be called to get the next updated id. This request may return nothing and LSC will wait until the next modification or its own stop
* **requestNameForClean**: node providing the SQL request name to get the full object for clean phase according to the datasets identifying it uniquely

Some of these parameters are mandatory depending on the corresponding synchronization mode: 

* in the synchronous mode: requestNameForList and requestNameForObject should be provided. requestNameForNextId is ignored.
* in the asynchronous mode: requestNameForNextId is mandatory whereas requestNameForList and requestNameForObject are ignored.

See then how configure :doc:`XML persistence map <persistence>` with iBatis.
