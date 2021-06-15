OC commands
========================================================================

Loop namespaces, based on a label

.. code-block:: bash

  for ns in $(oc get ns -l env=tst -o jsonpath='{.items[*].metadata.name}'); do echo $ns; done