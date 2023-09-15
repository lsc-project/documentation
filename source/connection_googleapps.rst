***********
Google Apps
***********

This section describe the Google Apps connection settings. 

Let's look at a sample:

.. code-block:: XML

    <lsc>
      <connections>
        <googleAppsConnection>
        	<name>googleapps</name>
        	<!-- The URL for Google Apps connection is the domain  -->
        	<url>domain.com</url>
        	<username>admin@domain.com</username>
        	<password>XXXXXXXXX</password>
        </googleAppsConnection>
      </connections>
    </lsc>

First, to be able to reference the connection in the service, it's mandatory to give it a unique name element so that it can be referenced later in services (here ``googleapps``).

* **url**: mandatory, this field shall contain a valid and well known domain name as accessed through the `http://www.google.com/a/domain.com <http://www.google.com/a/domain.com>`__
* **username**: mandatory, this field shall contain the email address that has administrative privileges on the previously specified domain
* **password**: mandatory, the password corresponding  to this email address

.. warning::

    At this time this connection supports neither OAuth nor Captcha reenforced login.
