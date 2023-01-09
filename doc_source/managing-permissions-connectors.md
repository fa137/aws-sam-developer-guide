# Managing resource permissions with AWS SAM connectors<a name="managing-permissions-connectors"></a>

**Topics**
+ [What are AWS SAM connectors?](#what-are-connectors)
+ [Example of the connector resource type](#what-are-connectors-example)
+ [Supported connections between source and destination resources](#supported-connector-resources)
+ [Supported data and event permission types](#supported-connector-permission-types)
+ [Benefits of AWS SAM connectors](#connector-benefits)
+ [Learn more](#connector-learn-more)
+ [Provide feedback](#connector-feedback)

## What are AWS SAM connectors?<a name="what-are-connectors"></a>

Connectors are an AWS Serverless Application Model \(AWS SAM\) abstract resource type, identified as `AWS::Serverless::Connector`, that provides a simple and secure method of provisioning permissions between your serverless application resources\. Using connectors, you define two resources and describe how data or events should flow between those resources\. AWS SAM then composes the access policies necessary to facilitate the required interactions\.

## Example of the connector resource type<a name="what-are-connectors-example"></a>

In this example, we use connectors to write data from an AWS Lambda function to an Amazon DynamoDB table\.

![\[A diagram of a Lambda function writing data to a DynamoDB table using AWS SAM connectors.\]](http://docs.aws.amazon.com/serverless-application-model/latest/developerguide/images/managing-connectors-example.png)

```
Transform: AWS::Serverless-2016-10-31
Resources:
  MyTable:
    Type: AWS::Serverless::SimpleTable
  MyFunction:
    Type: AWS::Serverless::Function
    Properties:
      Runtime: nodejs16.x
      Handler: index.handler
      InlineCode: |
        const AWS = require("aws-sdk");
        const docClient = new AWS.DynamoDB.DocumentClient();
        exports.handler = async (event, context) => {
          await docClient.put({
            TableName: process.env.TABLE_NAME,
            Item: {
              id: context.awsRequestId,
              event: JSON.stringify(event) 
            }
          }).promise();
        }
      Environment:
        Variables:
          TABLE_NAME: !Ref MyTable
  MyConnector:
    Type: AWS::Serverless::Connector
    Properties:
      Source:
        Id: MyFunction
      Destination:
        Id: MyTable
      Permissions:
        - Write
```

By defining a **source** resource, **destination** resource, and **permissions** between those resources, the `Write` data connection between the Lambda function and DynamoDB table is ready to be provisioned\. AWS SAM will automatically compose the necessary access policies required for this connection to work\. For more information about the resources generated by connectors, see [AWS CloudFormation resources generated when you specify AWS::Serverless::Connector](sam-specification-generated-resources-connector.md)\.

## Supported connections between source and destination resources<a name="supported-connector-resources"></a>

Connectors support a select number of source and destination resource connections\. For a list of supported connections, see [Supported source and destination resource types for connectors](reference-sam-connector.md#supported-connector-resource-types)\.

## Supported data and event permission types<a name="supported-connector-permission-types"></a>

Connectors support `Read` and `Write` data and event permission types between resources\. For more information, see [AWS SAM connector reference](reference-sam-connector.md)\.

## Benefits of AWS SAM connectors<a name="connector-benefits"></a>

By automatically composing the appropriate access policies between resources, connectors provide you the ability to author your serverless applications and focus on your application architecture without needing expertise in AWS authorization capabilities, policy language, and service\-specific security settings\. Therefore, connectors are a great benefit to developers who may be new to serverless development, or seasoned developers looking to increase their development velocity\.

## Learn more<a name="connector-learn-more"></a>

For more information about using AWS SAM connectors, see [AWS::Serverless::Connector](sam-resource-connector.md)\.

## Provide feedback<a name="connector-feedback"></a>

To provide feedback on connectors, [submit a new issue](https://github.com/aws/serverless-application-model/issues/new?assignees=&labels=area%2Fconnectors,stage%2Fneeds-triage&template=other.md&title=%28Feature%20Request%29) at the *serverless\-application\-model AWS GitHub repository*\.