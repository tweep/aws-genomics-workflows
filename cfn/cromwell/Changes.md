
Below a summary of changes in ``cromwell-resources.template.yaml``: 

2023-05-25:
== 

IAM change in cromwell-resources.template.yaml: 
==
  - Modfied Ec2InstanceRole [ AWS::IAM::Role ] 
  -  added new value to ManagedPolicyArns to allow access to converge s3 bucket:
     ManagedPolicyArns
     - "arn:aws:iam::353631943781:policy/converge-prd-7152-c56"   

Parameterized EC2 key pair:
==
  - added parameter pEc2KeyPair as parameter 
   - added property under ``rEC2Instance`` so key pair is used correctly  
```
  rEC2Instance:
    Type: AWS::EC2::Instance
    Properties: 
      KeyName: !Ref 'Ec2KeyPair'
```

Added new value to AWS SystemsManager to use hardened AMI 
== 
  - visit AWS SSM UI 
    Parameter Store : add new parameter to ssm which links path to AMI ID. 
  - added new allowed value to ``LatestAmazonLinuxAMI``:  
    ```
    LatestAmazonLinuxAMI:
    ...
      AllowedValues:
      - aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
      - cromwell/ami/amazon-linux-2 
    ``` 

Modified CROMWELL SYSTEM configuration for startup :
== 
  - io { memory-retry-error-keys = ["OutOfMemory","Killed"] }
  - workflow-heartbeats { write-failure-shutdown-duration = 20 minutes } 
