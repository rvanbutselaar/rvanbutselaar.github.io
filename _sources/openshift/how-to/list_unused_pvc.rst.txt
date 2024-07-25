List unused PVC's inside project using Python
========================================================================


.. code-block::

  import sys
  import datetime
  import time
  from kubernetes import config, client
  import urllib3urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
  def log(msg):
    print ("[ {} ] :: {}".format(datetime.datetime.now(), msg) )try:
    NAMESPACE = sys.argv[1]
  except IndexError:
    print("ERROR: missing some input")
    print("Usage: {} '<NAMESPACE>'".format(sys.argv[0]))
    sys.exit(1)#log ("Startup")
  config.load_kube_config()
  kube_client = client.CoreV1Api()#log ("find pvc's")
  all_pvc_list= []
  for pvc in kube_client.list_namespaced_persistent_volume_claim(NAMESPACE).items:
  #    print (pvc.metadata.name)
      all_pvc_list.append(pvc.metadata.name)#log ("find mounted pvc's")mounted_pvc_list = []
  for pod in kube_client.list_namespaced_pod(NAMESPACE).items:
      if str(pod.spec.volumes) != "None":
          for volume in pod.spec.volumes:
              if str(volume.persistent_volume_claim) != "None":
                  mounted_pvc_list.append(volume.persistent_volume_claim.claim_name)#log ("unmounted pvc's")
  unmounted_pvc = list(set(all_pvc_list) - set(mounted_pvc_list))
  output = ['   ' + sub for sub in unmounted_pvc]
  if output:
    print(NAMESPACE)
    print(*output, sep = "\n")