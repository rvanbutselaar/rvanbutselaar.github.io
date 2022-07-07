AWS EKS (Fargate)
========================================================================

Delete files inside S3 buckets

.. code-block:: bash

  eksctl create cluster --fargate --name rvb-tst --alb-ingress-access --without-nodegroup --dry-run

  eksctl create cluster --fargate --name rvb-tst --alb-ingress-access --without-nodegroup

  eksctl create fargateprofile --cluster rvb-tst \
    --region eu-central-1 --name ns1 --namespace ns1

  eksctl create fargateprofile --cluster rvb-tst \
    --region eu-central-1 --name ns2 --namespace ns2

  # test connections between namespaces:
  kubectl -n ns1 exec po/nginx -- curl -v nginx.ns1
  kubectl -n ns1 exec po/nginx -- curl -v nginx.ns2

  eksctl create fargateprofile --cluster rvb-tst \
    --region eu-central-1 --name ns-secure --namespace ns-secure

  # Configure Pod security groups:
  https://docs.aws.amazon.com/eks/latest/userguide/security-groups-for-pods.html#sg-pods-example-deployment
  # poort 10250 toestaan vanaf eks cluster SG om kubectl exec te laten werken

  # dit werkt niet:
  kubectl -n ns1 exec po/nginx -- curl -v my-app.ns-secure

  # dit werkt wel:
  kubectl -n ns-secure exec deploy/my-deployment -- curl -v my-app.ns-secure