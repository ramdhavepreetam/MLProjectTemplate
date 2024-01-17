## End to end Machine template

Starting: Bash
==============================================================================
Task         : Bash
Description  : Run a Bash script on macOS, Linux, or Windows
Version      : 3.231.5
Author       : Microsoft Corporation
Help         : https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/bash
==============================================================================
Generating script.
Script contents:
aws cloudformation package --template-file template.yaml --output-template-file packaged.yaml --s3-bucket ppd.test.pricfile.moderinization --metadata Build-Number=20240117.1
========================== Starting Command Output ===========================
/bin/bash /azuredevopsdownloads/_work/_temp/4c6ecc28-8c35-470b-8e1a-d20fe4191c42.sh

Unable to upload artifact lambda/dealer/price/ppd-custom-dealer-monthly-pricefile-scheduler.py referenced by Code parameter of CustomDealerMonthlyPricefileSchedulerFunction resource.
Unable to locate credentials

##[error]Bash exited with code '255'.
Finishing: Bash
