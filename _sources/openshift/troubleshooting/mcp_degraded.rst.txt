OCP 4 MachineConfigPool degraded
========================================================================

.. code-block:: bash

  syncRequiredMachineConfigPools: error pool master is not ready, retrying

#. ssh into the server
#. get the logs from the machine-config-daemon container using crictl logs
#. the diff is in the output, fix the file on the server
#. the server will be rebooted