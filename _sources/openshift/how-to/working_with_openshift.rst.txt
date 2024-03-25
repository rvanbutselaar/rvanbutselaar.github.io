Working with OpenShift
========================================================================

**Create a new deployment with name 'ronald' based on a public git repo**


.. code-block:: bash

  oc new-app --name ronald https://github.com/sclorg/cakephp-ex

**Creating secrets**

Source: https://access.redhat.com/documentation/en-us/openshift_container_platform/4.6/html/nodes/working-with-pods#nodes-pods-secrets

.. code-block:: bash

  oc create secret generic ronald-secret --from-literal=username=ronald --from-literal=password=topsecretpass

**Viewing secrets**

.. code-block:: bash

  oc get secret ronald-secret -ojsonpath='{.data.password}' | base64 -d
  oc get secret ronald-secret -ojsonpath='{.data.username}' | base64 -d

**Using secrets as an environment variable**

.. code-block:: bash

      containers:
      - env:
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              key: password
              name: ronald-secret
        - name: USERNAME
          valueFrom:
            secretKeyRef:
              key: username
              name: ronald-secret

**Using secrets as a volume mount**

.. code-block:: bash

      containers:
        volumeMounts:
        - mountPath: /etc/secret-volume
          name: secret-volume
          readOnly: true
      volumes:
      - emptyDir: {}
        name: secret-volume

**Expose a service as a route**

.. code-block:: bash

  oc expose service/ronald

**Creating a secured route (TLS)**

Source: https://access.redhat.com/documentation/en-us/openshift_container_platform/4.6/html/networking/configuring-routes

.. code-block:: bash

  openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt

  openssl x509 -in tls.crt -text -noout

  oc create route edge --service=ronald --cert=tls.crt --key=tls.key

  spec:
    tls:
      termination: edge
      certificate: |
        -----BEGIN CERTIFICATE-----
        -----END CERTIFICATE-----
      key: |
        -----BEGIN PRIVATE KEY-----
        -----END PRIVATE KEY-----


**htpasswd**

Source: https://access.redhat.com/documentation/en-us/openshift_container_platform/4.6/html/authentication_and_authorization/configuring-identity-providers#configuring-htpasswd-identity-provider

.. code-block:: bash

  sudo yum install httpd-tools

  # create file
  htpasswd -c -B -b users.htpasswd admin admin

  # update file, add additional user
  htpasswd -B -b users.htpasswd ronald ronald

  oc create secret generic htpass-secret-new --from-file=htpasswd=users.htpasswd -n openshift-config

  oc edit oauth cluster

.. code-block:: yaml

  apiVersion: config.openshift.io/v1
  kind: OAuth
  metadata:
    name: cluster
  spec:
    identityProviders:
    - name: my_htpasswd_provider
      mappingMethod: claim
      type: HTPasswd
      htpasswd:
        fileData:
          name: htpass-secret-new

**Modify / delete user (passwords)**

.. code-block:: bash

  oc get secret htpass-secret-new -ojsonpath={.data.htpasswd} -n openshift-config | base64 --decode > users.htpasswd

  # add new user
  htpasswd -b -B users.htpasswd <username> <password>

  # update password
  htpasswd -B -b users.htpasswd <username> <password>

  # delete user
  htpasswd -D users.htpasswd <username>

  # replace the secret
  oc create secret generic htpass-secret-new --from-file=htpasswd=users.htpasswd --dry-run=client -o yaml -n openshift-config | oc replace -f -

  # delete user inside openshift
  oc delete user kubeadmin
  oc delete identity my_htpasswd_provider:kubeadmin

**RBAC (create cluster-admin)**

.. code-block:: bash

  oc adm policy add-cluster-role-to-user cluster-admin admin

**RBAC (project access)**

.. code-block:: bash

  oc adm policy add-role-to-user admin ronald -n rvb-tst01

**Create custom RBAC rules**

Allow a user to get and list pods inside a project

.. code-block:: bash

  oc create role getpods --verb=get,list --resource=pods -n rvb-tst02

  oc adm policy add-role-to-user getpods ronald --role-namespace rvb-tst02 -n rvb-tst02

**Quota**

Source: https://access.redhat.com/documentation/en-us/openshift_container_platform/4.6/html/applications/quotas

.. code-block:: bash

  oc create quota ronald-quota --hard=cpu=1,memory=1G,pods=10,services=3,secrets=5

View the status, used vs available resources inside the resourcequota:

.. code-block:: bash

  oc get resourcequota

**Limit range**

Set default cpu & memory request / limits on projects

Source: https://access.redhat.com/documentation/en-us/openshift_container_platform/4.6/html/nodes/working-with-clusters#nodes-cluster-limit-ranges

.. code-block:: bash

  oc describe limits resource-limits

**Scaling**

.. code-block:: bash

  oc scale deploy ronald --replicas 3
  oc autoscale deployment ronald --min=1 --max=5

**Taints & tolerations**

Source: https://access.redhat.com/documentation/en-us/openshift_container_platform/4.6/html/nodes/controlling-pod-placement-onto-nodes-scheduling#nodes-scheduler-taints-tolerations-about_nodes-scheduler-taints-tolerations

.. code-block:: bash

  oc adm taint nodes crc-scmfr-master-0 dedicated=special-user:NoSchedule

Will result in the following on the node:

.. code-block:: yaml

  - effect: NoSchedule
    key: dedicated
    value: special-user

Now create a toleration on a deployment

.. code-block:: yaml

    spec:
      tolerations:
      - effect: NoSchedule
        key: dedicated
        operator: Equal
        value: special-user

**NodeSelector**

.. code-block:: bash

  oc label node crc-scmfr-master-0 zone1=east

.. code-block:: yaml

    spec:
      nodeSelector:
        zone1: east
      containers:
