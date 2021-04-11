# AWS Config auto-remediation rule

### About
  A cloudformation template for creating a custom AWS Config rule with auto-remediation lambda which ensures that `AWS Serial Console Access feature is always disabled`.
  
The rule is **compliant** when the AWS Serial Console Access feature:
- is disabled
- was enabled, but has been auto-remediated by the evaluation lambda.

The rule is **non-compliant** when the evaluation lambda:
- fails to disable the feature

---
**NOTE ABOUT LAMBDA RUNTIME**

AWS Serial Console Access is not currently supported by the boto3 version (1.16.31) in the default runtime, as a workaround, I am using an external boto3 layer provided by https://github.com/keithrozario/Klayers (works in all [supported regions](#supported-regions)).

The layer can be removed once AWS upgrades the boto3 version to at least 1.17.41.

---

### Supported regions
The AWS Serial Console Access feature is currently supported in the following regions: 
- US East (N. Virginia), US West (Oregon), US East (Ohio)
- Europe (Ireland), Europe (Frankfurt)
- Asia Pacific (Tokyo), Asia Pacific (Sydney), Asia Pacific (Singapore)

# Setup


### [prerequisite] Enabling AWS Config
Before launching a custom config rule template, enable AWS Config in one of the [supported regions.](#supported-regions)
To enable AWS Config, you can use a quickstart template `enable-config.yaml` that creates all essential resources. 

You can use the below quick launch stack button to create the stack in Ohio:

[![Launch Enable Config Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?templateURL=https://mzarkowski-static.s3-eu-west-1.amazonaws.com/enable_config.yaml&stackName=enable-config)


### Deploying a custom config rule
Create a new AWS Cloud Formation stack using the `ensure-serial-console-access-is-disabled.yaml` template. 

You can use the below quick launch stack button to create the stack in Ohio:

[![Launch Serial Console Access Disabled Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://us-east-2.console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?templateURL=https://mzarkowski-static.s3-eu-west-1.amazonaws.com/ensure-serial-console-access-is-disabled.yaml&stackName=ensure-serial-console-access-is-disabled) 

### Tests scenarios

**Scenario: 1**
- Given: AWS Serial Console Access is disabled.
- Then: Return COMPLIANT
- Evidence: 
![](https://github.com/mzarkowski/aws-config-custom-rule/blob/main/tests/scenario1.png)

**Scenario: 2**
- Given: AWS Serial Console Access had been enabled but was disabled succesfully. 
- Then: Return COMPLIANT
- Evidence: 
![](https://github.com/mzarkowski/aws-config-custom-rule/blob/main/tests/scenario2.png)

**Scenario: 3**
- Given: AWS Serial Console Access is enabled and lambda fails to disable it.
- Then: Return NON_COMPLIANT
- Evidence: 
![](https://github.com/mzarkowski/aws-config-custom-rule/blob/main/tests/scenario3.png)

