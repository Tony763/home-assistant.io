---
layout: page
title: "AWS Lambda"
description: "Instructions on how to invoke AWS Lambda functions from Home Assistant."
date: 2016-05-14 16:35
sidebar: true
comments: false
sharing: true
footer: true
logo: aws_lambda.png
ha_category: Notifications
ha_release: "0.20"
---

The `aws_lambda` notification platform enables invoking [AWS Lambda](https://aws.amazon.com/lambda/) functions.

## {% linkable_title Setup %}

For more information, please read the [AWS General Reference regarding Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html) to get the needed details. Also, check the [boto3 Documentation](http://boto3.readthedocs.io/en/latest/guide/configuration.html#shared-credentials-file) about the profiles and the [AWS Regions and Endpoints Reference](https://docs.aws.amazon.com/general/latest/gr/rande.html#pol_region) for available regions.

## {% linkable_title Configuration %}

To use this notification platform in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
notify:
  - name: NOTIFIER_NAME
    platform: aws_lambda
    aws_access_key_id: AWS_ACCESS_KEY_ID
    aws_secret_access_key: AWS_SECRET_ACCESS_KEY
    region_name: 'us-east-1'
```

{% configuration %}
aws_access_key_id:
  description: Your AWS Access Key ID. If provided, you must also provide an `aws_secret_access_key` and must **not** provide a `profile_name`.
  required: Required if aws_secret_access_key is provided
  type: string
aws_secret_access_key:
  description: Your AWS Secret Access Key. If provided, you must also provide an `aws_access_key_id` and must **not** provide a `profile_name`.
  required: Required if aws_access_key_id is provided
  type: string
profile_name:
  description: A credentials profile name.
  required: false
  type: string
region_name:
  description: The region identifier to connect to.
  required: true
  default: "`us-east-1`"
  type: string
name:
  description: Setting the optional parameter `name` allows multiple notifiers to be created. The notifier will bind to the service `notify.NOTIFIER_NAME`.
  required: false
  default: notify
  type: string
context:
  description: An optional dictionary you can provide to pass custom context through to the Lambda function. The `context` dictionary (if any) is combined with the same data available at the `/api/config` HTTP API route.
  required: false
  type: string
{% endconfiguration %}

### {% linkable_title Usage %}

AWS Lambda is a notify platform and thus can be controlled by calling the notify service [as described here](/components/notify/). It will invoke a Lambda for all targets given in the notification payload. A target can be formatted as a function name, an entire ARN ([Amazon Resource Name](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)) or a partial ARN. For more information, please see the [boto3 docs](http://boto3.readthedocs.io/en/latest/reference/services/lambda.html#Lambda.Client.invoke).

The Lambda event payload will contain everything passed in the service call payload. Here is an example payload that would be sent to Lambda:

```json
{
  "title": "Test message!",
  "target": "arn:aws:lambda:us-east-1:123456789012:function:ProcessKinesisRecords",
  "data": {
    "test": "okay"
  },
  "message": "Hello world!"
}
```

The context will look like this:

```json
{
  "hass": {
    "components": ["recorder", "logger", "http", "logbook", "api", "frontend"],
    "latitude": 44.1234,
    "location_name": "Home",
    "longitude": 5.5678,
    "unit_system": "metric",
    "time_zone": "Europe/Zurich",
    "version": "0.20.0.dev0"
  },
  "custom": {
    "two": "three",
    "test": "one"
  }
}
```
