oc commands
========================================================================

Loop namespaces, based on a label

.. code-block:: bash

  for ns in $(oc get ns -l env=tst -o jsonpath='{.items[*].metadata.name}'); do echo $ns; done

Delete failed openshift logging curator jobs:

.. code-block:: bash

  oc delete job -n openshift-logging $(oc get jobs -n openshift-logging -o=jsonpath='{.items[?(@.status.failed>0)].metadata.name}')