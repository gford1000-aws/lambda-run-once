# Lambda Run Once

Currently AWS CloudWatch (July 2017) does not provide a ScheduledRule for one-time issuing of an Event to trigger a Lambda invocation.

This Cloudformation script provides such a mechanism, using a normal ScheduledRule that triggers the target Lambda indirectly.  The target Lambda is invoked asynchronously by another Lambda function that is triggered by the ScheduledRule.  The triggered Lambda function then disbles the ScheduledRule so that it is never triggered again.

To achieve this, the triggered Lamdba function must have the permissions to both invoke the target Lambda function and to disable the ScheduledRule.  The script creates an IAM role for the triggered Lambda function with these priviledges only.

The target Lambda can be configured completely independently - since it is run from an event send by AWS Lambda.  For example, this means that the target Lambda can run within a private VPC (i.e. no access to the internet) if this is required.

The script creates the following:

![alt text](https://github.com/gford1000-aws/lambda-run-once/blob/master/lamda%20run%20once.png "Script per designer")


Notes:

1. If the target Lambda is running in a private VPC, then two invocations may be observed rather than one.  This is due to the time taken to create the ENI and attach them to the Lambda container, which will cause the target Lambda function to time out.  AWS Lambda will detect this and reissue the event, which should then complete successfully.


## Arguments

| Argument           | Description                                                               |
| ------------------ |:-------------------------------------------------------------------------:|
| DelayInterval      | The interval before the specified Lambda is to be executed                |
| LambdaArn          | The Arn of the Lambda to be executed (must be in the same region)         |


## Outputs

There are no outputs from this script.


## Licence

This project is released under the MIT license. See [LICENSE](LICENSE) for details.
