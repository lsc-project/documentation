******
Audits
******

Audits allow you to export applied modifications in different formats.

.. warning::

    Audits are a work in progress, and may not work in this version. For production use, configure :doc:`logback.xml file <logging>`.

The audits configuration are done in the ``<audits>`` section:

.. code-block:: XML

    <lsc>
      <audits>
         <audit>
         ...
         </audit>
         <audit>
         ...
         </audit>
      </audits>
    </lsc>

.. note::

    You can still configure audit outputs in the :doc:`logback.xml file <logging>`.

CSV
===

Parameters are:

.. code-block::

    lsc>audits>audit>csv>filename = /tmp/audit.csv
    lsc>audits>audit>csv>append = true (false by default)
    lsc>audits>audit>csv>operations = create, delete
    lsc>audits>audit>csv>attrs = cn;dn
    lsc>audits>audit>csv>separator = ; (optional)
    lsc>audits>audit>csv>outputHeader = true (true by default)

LDIF
====

Parameters are:

.. code-block::

    lsc>audits>audit>ldif>filename = /tmp/audit.ldif
    lsc>audits>audit>ldif>append = true (false by default)
    lsc>audits>audit>ldif>operations = create, delete
    lsc>audits>audit>ldif>logOnlyLdif = true (true by default)

