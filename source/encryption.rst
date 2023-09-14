******************
Encryption options
******************

The encryption options are used to provide a two-way encryption mechanism required to protect sensible information.

They are used through the :doc:`SecurityUtils library <syncrulessecurity>`.

Symmetric encryption can be configured via the 3 following parameters in configuration file:

* **lsc>securityencryption>keyfile**: the path to the file used to encrypt/decrypt data. Defaults to ``lsc.key`` in the current configuration directory.
* **lsc>security>encryption>algorithm**: the algorithm to use. Defaults to AES.
* **lsc>security>encryption>strength**: the strength in bits. Defaults to 128.

Please find below a sample configuration of these options:

.. code-block:: XML

    <lsc>
      <security>
        <encryption>
          <keyfile>$LSC_HOME/etc/lsc.key</keyfile>
          <algorithm>AES</algorithm>
          <strength>128</strength>
        </encryption>
      </security>
    </lsc>
