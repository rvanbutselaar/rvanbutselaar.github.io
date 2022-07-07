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


ECR

.. code-block:: bash

  aws ecr get-login-password --region eu-central-1 | docker login ******.dkr.ecr.eu-central-1.amazonaws.com/azrt --username AWS --password-stdin
  aws ecr describe-images --repository-name azrt
  aws ecr batch-delete-image --repository-name azrt --image-ids imageTag=0.1.3
  
CodeCommit

.. code-block:: bash
  
  for r in $(aws codecommit list-repositories| jq -r '.repositories[].repositoryName'); do git clone codecommit::eu-central-1://$r; done

