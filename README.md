# The build pipeline only zips the artifacts and uploads to S3.
# Terraform only does deploy, not build, so is not used here

stages:
- stage: PackageDeploy
  variables:
    S3Bucket: ppd.test.pricfile.moderinization
    AWS_Region: us-west-2
  jobs:
  - job: CreateAndUpload
    pool:
      name: PPD-AWS
      vmImage: 'ubuntu-latest'
    steps:
      - task: Bash@3
        inputs:
          noProfile: false
          targetType: 'inline'
          script: |
            aws cloudformation package --template-file template.yaml --output-template-file packaged.yaml --s3-bucket $(S3Bucket) --metadata Build-Number=$(Build.BuildNumber)
      
      - task: CopyFiles@2
        inputs:
          SourceFolder: '$(Build.SourcesDirectory)'
          Contents:   '**'
          TargetFolder: '$(Build.ArtifactStagingDirectory)'
      
      - task: PublishPipelineArtifact@1
        inputs:
          targetPath: '$(Build.ArtifactStagingDirectory)'
          artifact: 'pricefilemodernization'
trigger:
  branches: 
    include: [main]
  
