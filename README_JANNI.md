
# What CFN templates are used ? 


  * gwfcore-root.template.yaml  
    Parent template, invokes all other templates to create a nested stack.

  * gwfcore-batch.template.yaml  
    Creates compute environment and queues.

  * gwfcore-code.template.yaml 
    Creates AWS CodeCommit repos and CodeBuild projects for Genomics Workflows Core assets and artifacts. 
    These are used during run-time - so things can be changed w/o drawing the full environment down.

  * gwfcore-efs.template.yaml 
    Creates EFS file system and mount targets to a list of subnets.

  * gwfcore-fsx.template.yaml 
    Creates fast FSX file system. 

  * gwfcore-iam.template.yaml 
    Creates IAM related policies, roles and permissions to access resources.

  * gwfcore-launch-template.template.yaml 
    EC2 Instance Launch configuration. 


  * gwfcore-s3.template.yaml
    Creates bucket to store results.
