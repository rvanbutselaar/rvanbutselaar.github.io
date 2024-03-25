OCP 4 list in use persistentVolumeClaim per pod
========================================================================

.. code-block:: bash

  oc get po -o json | jq -c '.items[] | {name: .metadata.name, namespace: .metadata.namespace, claimName:.spec.volumes[] | select( has ("persistentVolumeClaim") ).persistentVolumeClaim.claimName }'

Example output:

.. code-block:: bash

  {"name":"pod-1-h7hfv","namespace":"default","claimName":"storage"}