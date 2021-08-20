OCP 4 tune nodes inside cluster
========================================================================

Get the memory and cpu requests:

.. code-block:: 

  container_spec_cpu_shares{namespace!~"(openshift-.*|kube-.*)"}
  sum(kube_pod_container_resource_requests_memory_bytes{namespace!~"(openshift-.*|kube-.*)"})

Based on the output you can determine how many nodes you need.

.. list-table:: Overview differences between 2 AWS spot instance types
  :header-rows: 1

  * - Type
    - Uren in maand
    - Prijs per uur
    - Maand prijs
  * - t3.xlarge
    - 720
    - 0,0566
    - 40,752
  * - r4.xlarge
    - 720
    - 0,0658
    - 47,376