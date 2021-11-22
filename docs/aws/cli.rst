AWS CLI
========================================================================

Delete files inside S3 buckets

.. code-block:: bash

  aws s3 rm s3://bucket1 --recursive --dryrun
  aws s3 rm s3://bucket1 --recursive

  for i in $(aws s3api list-buckets --output  text|awk '{print $3}'); do aws s3 rm s3://${i} --recursive --dryrun; done
  for i in $(aws s3api list-buckets --output  text|awk '{print $3}'); do aws s3 rm s3://${i} --recursive; done


List and delete CloudFormation stacks:

.. code-block:: bash

  aws cloudformation list-stacks
  aws cloudformation delete-stack --stack-name stack