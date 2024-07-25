AWS list available instance type in AZ
========================================================================

Delete files inside S3 buckets

.. code-block:: python

  import boto3
  import logging

  # logger config
  logger = logging.getLogger()
  logging.basicConfig(level=logging.INFO,

                      format='%(asctime)s: %(levelname)s: %(message)s')

  ec2_client = boto3.client('ec2')
  sns_client = boto3.client('sns')

  logger.info("Checking if c6a.xlarge is available in eu-central-1a")

  response = ec2_client.describe_instance_type_offerings(
      LocationType='availability-zone',
      Filters=[{
          'Name': 'location',
          'Values':['eu-central-1a']},
          {'Name': 'instance-type',
          'Values': ['c6a.xlarge'],
          }])

  if len(response['InstanceTypeOfferings']) > 0:
      sns_client.publish(TopicArn="arn:aws:sns:eu-central-1:ACCOUNT_ID:email-test",
                  Message="c6a.xlarge is available in eu-central-1a",
                  Subject="c6a.xlarge is available in eu-central-1a")
      for offering in response['InstanceTypeOfferings']:
          print(offering['InstanceType'])
  else:
      sns_client.publish(TopicArn="arn:aws:sns:eu-central-1: ACCOUNT:email-test",
                  Message="c6a.xlarge is NOT available in eu-central-1a",
                  Subject="c6a.xlarge is NOT available in eu-central-1a")