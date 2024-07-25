OCP 4 update CA bundle
========================================================================

First you can update the global CA bundle using:

.. code-block:: bash

  oc create configmap user-ca-bundle --from-file=ca-bundle.crt=ca-bundle.crt --dry-run -o yaml| oc replace -f-

And, if you need to you can update the container runtime (cri-o) ca bundle using:

.. code-block:: bash

  oc create configmap registry-root-ca --from-file=registry.nl=ca-bundle.crt --dry-run -o yaml| oc -n openshift-config replace -f-

In both cases you need to create a file containing all the CA's you want to include. You can add multiple in a single file.