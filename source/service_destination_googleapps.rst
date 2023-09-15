*******************************
Google Apps destination service
*******************************

This service will synchronize identities to Google Apps. It requires a :doc:`Google Apps <connection_googleapps>` connection.

Let's find below a sample and the parameters description:

.. code-block:: XML

    <lsc>
      <tasks>
        <task>
          <googleAppsDestinationService>
            <name>googleapps-dst-service</name>
            <connection reference="googleapps" />
            <apiCategory>UserAccounts</apiCategory>
            <quotaLimitInMb>1000</quotaLimitInMb>
          </googleAppsDestinationService>
        </task>
      </tasks>
    </lsc>

The Google Apps destination service should be configured by using the following settings:

* **name**: mandatory, it should contain any value that will uniquely identify this service regarding a task
* **connection**: this node should not contain any element, just a ``reference`` attribute which is used to look for the corresponding connection with this name (as sub-element of the connection element)
* **apiCategory**: mandatory, ``UserAccounts`` for now.
* **quotaLimitInMb**: self-explanatory, can be overriden at runtime by specifying a dataset with a customized value regarding a particular group for example

.. note::

    API "Groups" and "OrganizationalUnits" will be supported later.

Destination fetched attributes are built in the service, you can use datasets for:

* uid
* mail
* sn
* isSuspended
* userPassword
* givenName
* modifyTimestamp
* isIpWhitelisted
* id
* createTimestamp
* quotaInMb
* isAgreedToTerms
* userPasswordHash
* isAdmin

The pivot attribute is uid, this is also the main identifier.

