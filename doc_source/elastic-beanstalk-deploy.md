# AWS Elastic Beanstalk Deploy Application Task<a name="elastic-beanstalk-deploy"></a>

## Synopsis<a name="synopsis"></a>

Deploys a new version of an application to an Elastic Beanstalk environment associated with the application\.

## Description<a name="description"></a>

With Elastic Beanstalk, you can quickly deploy and manage applications in the AWS Cloud without worrying about the infrastructure that runs those applications\. Elastic Beanstalk reduces management complexity without restricting choice or control\. You simply upload your application, and Elastic Beanstalk automatically handles the details of capacity provisioning, load balancing, scaling, and application health monitoring\.

This task can deploy ASP\.NET applications \(as Web Deploy archives\), ASP\.NET Core applications, an existing built application or a previously registered application version using the Elastic Beanstalk Create Version task\.

## Parameters<a name="parameters"></a>

You can set the following parameters for the task\. Required parameters are noted by an asterisk \(\*\)\. Other parameters are optional\.

### Display name\*<a name="display-name"></a>

The default name of the task instance, which can be modified: Deploy to Elastic Beanstalk

### AWS Credentials<a name="aws-credentials"></a>

Specifies the AWS credentials to be used by the task in the build agent environment\.

You can specify credentials using a service endpoint \(of type AWS\) in the task configuration or you can leave unspecified\. If unspecified the task will attempt to obtain credentials from the following sources in order:
+ From task variables named *AWS\.AccessKeyID*, *AWS\.SecretAccessKey* and optionally *AWS\.SessionToken*\.
+ From credentials set in environment variables in the build agent process\. When using environment variables in the build agent process you may use the standard AWS environment variables: *AWS\_ACCESS\_KEY\_ID*, *AWS\_SECRET\_ACCESS\_KEY* and optionally *AWS\_SESSION\_TOKEN*\.
+ If the build agent is running on an Amazon EC2 instance, from the instance metadata associated with the EC2 instance\. For credentials to be available from EC2 instance metadata the instance must have been started with an instance profile referencing a role granting permissions to the task to make calls to AWS on your behalf\. See [IAMRolesForEC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html) for more information\.

### AWS Region<a name="aws-region"></a>

The AWS region code \(us\-east\-1, us\-west\-2 etc\.\) of the region containing the AWS resource\(s\) the task will use or create\. For more information, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the Amazon Web Services General Reference\.

If a region is not specified in the task configuration the task will attempt to obtain the region to be used using the standard AWS environment variable *AWS\_REGION* in the build agent process's environment\. Tasks running in build agents hosted on Amazon EC2 instances \(Windows or Linux\) will also attempt to obtain the region using the instance metadata associated with the EC2 instance if no region is configured on the task or set in the environment variable\.

 **Note:** The regions listed in the picker are those known at the time this software was released\. New regions that are not listed may still be used by entering the *region code* of the region \(for example *us\_west\_2*\)\.

### Application Name\*<a name="application-name"></a>

The name of the Elastic Beanstalk application\.

### Environment Name\*<a name="environment-name"></a>

The name of the Elastic Beanstalk environment that will run the application\.

An environment represents the AWS resources \(e\.g\., load balancer, Auto Scaling group, and Amazon EC2 instances\) created specifically to run your application\.

### Deployment Bundle Type\*<a name="deployment-bundle-type"></a>

The type of application bundle to deploy\. You can select from
+ ASP\.NET: the deployment bundle is expected to be a Web Deploy archive, built previously, which the task will upload\.
+ ASP\.NET Core: the deployment bundle will be created by the task \(using the `dotnet publish` command line tool\) and uploaded\.
+ Existing deployment bundle: choose to deploy a bundle that has been built and uploaded previously to Amazon S3\.
+ Existing application version: choose to deploy a revision previously registered with Elastic Beanstalk\.

### Web Deploy Archive<a name="web-deploy-archive"></a>

Required if `Deployment Bundle Type` is set to **ASP\.NET**\. The path to the web deploy archive containing the application to deploy to Elastic Beanstalk\.

### Published Application Path<a name="published-application-path"></a>

Required if `Deployment Bundle Type` is set to **ASP\.NET Core**\. The output location where the `dotnet publish` command in your previous build steps placed the deployment artifact\(s\) to be published\. Configure using either:
+ The path to the output folder containing the artifacts\. Use this if the `dotnet publish` command in your build was configured to not create a zip file of the published application\.
+ The path and filename of the zip file containing the artifacts\. Use this if the `dotnet publish` command in your build was configured to create a zip file of the application artifacts\.

### Deployment Bundle Bucket<a name="deployment-bundle-bucket"></a>

Required if `Deployment Bundle Type` is set to **Existing deployment bundle**\. The name of the Amazon S3 bucket containing the revision bundle to deploy\.

### Deployment Bundle Object Key<a name="deployment-bundle-object-key"></a>

Required if `Deployment Bundle Type` is set to **Existing deployment bundle**\. The Amazon S3 object key of the revision bundle file to be deployed\.

### Version Label<a name="version-label"></a>

Version label for the new application revision\. If not specified the task will construct a version label based on the current date and time, expressed in milliseconds \(for example *v20171120222623*\)\.

### Version Label Output Variable<a name="version-label-output-variable"></a>

Optional variable name to which the version label for the revision will be stored on conclusion of the task\. This is useful when `Version Label` is not specified and the task generates a version label for the revision\. You can refer to this variable in subsequent build steps to obtain the deployed version label\.

## Task Permissions<a name="task-permissions"></a>

This task requires permissions to call the following AWS service APIs \(depending on selected task options, not all APIs may be used\):
+ elasticbeanstalk:CreateApplicationVersion
+ elasticbeanstalk:CreateStorageLocation
+ elasticbeanstalk:DescribeApplications
+ elasticbeanstalk:DescribeEnvironments
+ elasticbeanstalk:DescribeEvents
+ elasticbeanstalk:UpdateEnvironment

The task also requires permissions to upload your application content to the specified Amazon S3 bucket\. Depending on the size of the application bundle, either PutObject or the S3 multi\-part upload APIs may be used\.