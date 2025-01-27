
GWDI Setup
==

We're follwing the setup of two stack types, which are described in [README](aws-genomics-workflows/src/templates/README.md). 

We got to create two stacks:  

- a stack for the cromwell server 
- a stack with nested stacks 


Create nested stacks
==
```
export STACK_NAME=uat

aws cloudformation create-stack \
    --region us-west-2 \
    --stack-name $STACK_NAME \
    --template-url $TEMPLATE_ROOT_URL/gwfcore/gwfcore-root.template.yaml \
    --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
    --parameters file://gwdi.parameters.uat.json 

        ParameterKey=VpcId,ParameterValue=<vpc-id> \
        ParameterKey=SubnetIds,ParameterValue=\"<subnet-id-1>,<subnet-id-2>,...\" \
        ParameterKey=ArtifactBucketName,ParameterValue=<dist-bucketname> \
        ParameterKey=TemplateRootUrl,ParameterValue=$TEMPLATE_ROOT_URL \
        ParameterKey=S3BucketName,ParameterValue=<store-buketname> \
        ParameterKey=ExistingBucket,ParameterValue=false
```

Create cromwell server stack  
== 

``` 
   cd aws-genomics-workflows/cfn/ 

   aws cloudformation create-stack \ 
   --stack-name cfn-ehivex-JGT-computeEnv-on-demand-v1-0-1\
   --template-body file://../src/templates/cromwell/cromwell-resources.template.yaml \
       --parameters file://cromwell-uat.json


```
