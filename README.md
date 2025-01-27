# Genomics Workflows on AWS

[![Build Status](https://travis-ci.com/aws-samples/aws-genomics-workflows.svg?branch=master)](https://travis-ci.com/aws-samples/aws-genomics-workflows)

This repository is the source code for [Genomics Workflows on AWS](https://docs.opendata.aws/genomics-workflows).  It contains markdown documents that are used to build the site as well as source code (CloudFormation templates, scripts, etc) that can be used to deploy AWS infrastructure for running genomics workflows.

If you want to get the latest version of these solutions up and running quickly, it is recommended that you deploy stacks using the launch buttons available via the [hosted guide](https://docs.opendata.aws/genomics-workflows).

If you want to customize these solutions, you can create your own distribution using the instructions below.

## Creating your own distribution

Clone the repo

```bash
git clone https://github.com/aws-samples/aws-genomics-workflows.git
```

Create an S3 bucket in your AWS account to use for the distribution deployment

```bash
aws s3 mb <dist-bucketname>
```

Create and deploy a distribution from source

```bash
cd aws-genomics-workflows
bash _scripts/deploy.sh --deploy-region <region> --asset-profile <profile-name> --asset-bucket s3://<dist-bucketname> test
```

This will create a `dist` folder in the root of the project with subfolders `dist/artifacts` and `dist/templates` that will be uploaded to the S3 bucket you created above.

Use `--asset-profile` option to specify an AWS profile to use to make the deployment.

**Note**: the region set for `--deploy-region` should match the region the bucket `<dist-bucketname>` is created in.

You can now use your deployed distribution to launch stacks using the AWS CLI. For example, to launch the GWFCore stack:

```bash
TEMPLATE_ROOT_URL=https://<dist-bucketname>.s3-<region>.amazonaws.com/test/templates

aws cloudformation create-stack \
    --region <region> \
    --stack-name <stackname> \
    --template-url $TEMPLATE_ROOT_URL/gwfcore/gwfcore-root.template.yaml \
    --capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND \
    --parameters \
        ParameterKey=VpcId,ParameterValue=<vpc-id> \
        ParameterKey=SubnetIds,ParameterValue=\"<subnet-id-1>,<subnet-id-2>,...\" \
        ParameterKey=ArtifactBucketName,ParameterValue=<dist-bucketname> \
        ParameterKey=TemplateRootUrl,ParameterValue=$TEMPLATE_ROOT_URL \
        ParameterKey=S3BucketName,ParameterValue=<store-buketname> \
        ParameterKey=ExistingBucket,ParameterValue=false

```

## Shared File System Support

Amazon EFS is supported out of the box for `GWFCore` and `Nextflow`. You have two options to use EFS.

1. **Create a new EFS File System:** Be sure to have `CreateEFS` set to `Yes` and also include the total number of subnets.
2. **Use an Existing EFS File System:** Be sure to specify the EFS ID in the `ExistingEFS` parameter. This file system should be accessible from every subnet you specify.

Following successful deployment of `GWFCore`, when creating your Nextflow Resources, set `MountEFS` to `Yes`.

## Building the documentation

The documentation is built using mkdocs.

Install dependencies:

```bash
$ conda env create --file environment.yaml
```

This will create a `conda` environment called `mkdocs`

Build the docs:

```bash
$ conda activate mkdocs
$ mkdocs build
```

## Incremental changes to cloudformation stack.

Depending on the changes, it may not be required for you to delete and re-create the stack each time 

To do incremental updates: 
1) sync your templates to the template root url
2) Create a change set and double check that the changes are intended.
3) Execute change set
```
# sync template
aws s3 sync ./src/templates s3://gwfcore-congee-dev/test/templates/

# create change set
aws cloudformation create-change-set --stack-name gwfcore-dev  \
--capabilities CAPABILITY_IAM CAPABILITY_AUTO_EXPAND  \
--parameters file://parameters/congee.dev.parameters.json \
--template-url  "${TEMPLATE_ROOT_URL}"gwfcore/gwfcore-root.template.yaml \
--change-set-name add-batch-queue-nested     --include-nested-stacks

# to view changeset, go to UI (recommeneded or 
aws cloudformation describe-change-set --change-set-name <changeset_arn>

# to execute change set click execute change set on ui or 
aws cloudformation execute-change-set --change-set-name <changeset_arn>

```



## License Summary

This library is licensed under the MIT-0 License. See the LICENSE file.
