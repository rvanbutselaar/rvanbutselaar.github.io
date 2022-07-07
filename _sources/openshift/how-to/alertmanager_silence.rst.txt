Alertmanager silence
========================================================================

Add silence based on specific alertname and severity.

.. code-block:: bash

  oc -n openshift-monitoring exec alertmanager-main-0 -- sh -c 'for severity in info; do \
   echo $severity; \
   amtool silence add alertname=UpdateAvailable \
     --alertmanager.url=http://localhost:9093 \
     --comment="via tickets controleren we op updates" \
     --duration="365d"
    done'

Disable all alerting during OpenShift upgradess

.. code-block:: bash

  oc -n openshift-monitoring exec alertmanager-main-0 -- sh -c 'for severity in none info warning minor major critical; do \
   echo $severity; \
   amtool silence add severity=${severity} \
     --alertmanager.url=http://localhost:9093 \
     --comment="openshift upgrade" \
     --duration="3h"
    done'

