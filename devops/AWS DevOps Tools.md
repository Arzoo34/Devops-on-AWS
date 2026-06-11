![[Pasted image 20260611090446.png]]
## AWS CodePipeline

AWS CodePipeline is a continuous delivery service that enables you to model, visualize, and automate the steps required to release your software. 

**Why use?**

With CodePipeline you can:

- Capture and visualize your pipeline, run it, view real-time status, and retry failed actions.
    
- Automate your release processes, eliminating human error, speed up delivery, and improve the quality of the release. 
    
- Establish consistency in the release.
    
- Incorporate your source, build, and deploy tools.
    
- View pipeline history details.
    
- Integrate with third-party and AWS tools to build, test, and deploy your code when notified of a code change. 
    

**Monitoring**

You can monitor your pipeline in a number of ways to assure its performance, reliability, and availability, and to find ways to improve it. You can monitor the pipeline directly from the AWS CodePipeline console, the command line interface (CLI), use Amazon EventBridge, or AWS CloudTrail.

**Security**

Security is an important part of any pipeline. CodePipeline supports resource-level permissions, enabling you to specify which user can perform what action on the pipeline. Some users might have read-only access to the pipeline, while others might have access to a particular stage or action within a stage. For more information about security, see [Security in AWS CodePipeline(opens in a new tab)](https://docs.aws.amazon.com/codepipeline/latest/userguide/security.html).

**How it works**

With CodePipeline, you model your release process with a number of stages and actions. 

CodePipeline breaks up your release workflow into a series of stages, such as code, build, and test. Each stage can have a number of actions that need to be performed. 

- Actions are tasks that can run in sequence or in parallel to each other. Actions are associated with a service provider that runs the action, or they can require user intervention. Service providers can be AWS services (like CodeBuild, Amazon Simple Storage Service (Amazon S3), AWS Lambda, and AWS CloudFormation), or third-party services (like Jenkins and TeamCity). 
    
- Action types include: 
    
    - Source (where the source is stored)
        
    - Build (how to build the application)
        
    - Test  how to test the application)
        
    - Deploy (how to deploy the application)
        
    - Approval (manual approval and notifications)
        
    - Invoke (Invoke a custom function).
## AWS CodeBuild

AWS CodeBuild is a fully managed build service that automatically compiles source code, runs tests, and produces software packages. 

**Why use?**

With CodeBuild you can:

- Eliminate the need to set up, patch, update, and manage your own build servers, since CodeBuild is fully managed.
    
- Automatically compile source code, run tests, and produce build artifacts.
    
- Specify build commands to run at each phase of the build.
    
- Process multiple builds concurrently, for example, developers can continuously build and test their code, catch errors early, and correct them early.
    
- Leverage out of the box preconfigured build environments (such as .NET Core, Java, Ruby, Python, Go, NodeJS, Android and Docker). Build environments contain the operating system, programming language runtime, and build tools (such as Apache Maven, Gradle). You can also provide custom build environments suited to your needs by means of Docker images.
    
- Pull source code from Amazon S3, GitHub, GitHub Enterprise, and Bitbucket.
    
-  Integrate CodeBuild with Jenkins to simplify the build process.
    

**Monitoring**

You can monitor the build through the CodeBuild console, CloudWatch Logs and a number of other ways.

**Security**

Build artifacts are encrypted and you control access to your build projects through resource-level permissions in AWS Identity and Access Management (IAM) policies. For more information about security, see [Security in AWS CodeBuild.(opens in a new tab)](https://docs.aws.amazon.com/codebuild/latest/userguide/security.html)

**How it works**

CodeBuild uses information provided by a build project to create a build environment (a provisioned Docker container created fresh on every build) where it then runs the build. Here is an overview of the steps that CodeBuild takes during the build.

1. CodeBuild uses a build project to run a build. A build project contains information needed to run the build, such as source repository location, runtime environment, build command, and where to store the build output. To access CodeBuild, use either the AWS Management Console, AWS Command Line Interface (AWS CLI), AWS SDKs, or CodePipeline.
    
2. CodeBuild uses the build project to create a build environment.
    
3. CodeBuild downloads the code and uses a build specification (buildspec) file to run a build. A buildspec is a collection of build commands that will run at each phase of the build (like installing tool packages, running tests, or packaging your code) and related settings. 
    
4. Any build output is uploaded to an Amazon S3 bucket and able to configure notifications.
    
5. Build output is streamed to the service console, and CloudWatch Logs.
    
6. Monitor the progress of the build through CloudWatch or other services.

## AWS CodeDeploy

AWS CodeDeploy is a fully managed service that automates your software deployments, allowing you to deploy reliably and rapidly. It automates code deployment to a variety of compute services, including Amazon Elastic Compute Cloud (Amazon EC2), AWS Fargate, AWS Lambda, or on-premises servers. 

**Why use?**

With CodeDeploy you can:

- Deploy server, serverless, or container applications.
    
- Automate deployments and eliminate the need for manual, error-prone operations. With CodeDeploy you can reliably and rapidly release new features and updates. 
    
- Deploy on a variety of compute platforms including: AWS Lambda, Amazon ECS, Amazon EC2, or on-premises. You can even configure CodeDeploy to deploy to an Amazon EC2 Auto Scaling group, which will prepare the environment before traffic is sent to it.
    
- Concurrently deploy to one or multiple instances as the service scales to fit your needs.
    
- Minimize production downtime for your application by specifying if an update will be applied on an existing instance, or a newly provisioned environment that will replace the previous environment. You can also control how to handle the traffic-shifts from older to new versions. For example, if your application needs at least 50% of the instances in a deployment group to be up and serving traffic, you can specify that in your deployment configuration so that a deployment does not cause downtime.
    
- Automatically (or through user intervention) stop an unsuccessful deployment and roll back your deployment to a previous version.  
    

**Monitoring**

AWS provides various tools that you can use to monitor CodeDeploy, including CloudWatch alarms, the CodeDeploy console and more.

**Security** 

You can configure CodeDeploy to meet your security and compliance objectives. For more information on security, see [Security in AWS CodeDeploy.(opens in a new tab)](https://docs.aws.amazon.com/codedeploy/latest/userguide/security.html)

**How it works**

To automate the deployment to the appropriate compute resources, CodeDeploy needs to know which files to copy, what scripts to run, and where to deploy.  

The concept of an _application_ is used by CodeDeploy to ensure it knows what to deploy (correct revision of code), where to deploy (deployment group), and how to deploy (deployment configuration).

- **Code** 
    
    - Identify the correct version (revision) of the code.
        
    - With the code, you provide an application specification file (AppSpec file) which is used to manage each deployment. During deployment, CodeDeploy looks for your AppSpec file in the root directory of the application's source. 
        
    - The AppSpec file specifies where to copy the code and how to get it running. For example, it tells CodeDeploy how to stop the application if it is already running, how to install the code, what command to run before and after the code is installed, and how to get the application running again.
        
- **Deployment group**
    
    - A _deployment group_ specifies the deployment targeted environment. The information it contains is specific to the target compute platform: AWS Lambda, Amazon ECS, Amazon EC2, or on-premises. For example, Amazon ECS lets you specify the Amazon ECS service, load balancer and more. For Amazon EC2, it is a logical group of deployment target instances or physical environments.
        
    - A CodeDeploy application can have one or more deployment groups.
        
    - Security needs to be assigned so the environment can communicate with CodeDeploy.
        
    - The CodeDeploy agent is needed if you are deploying to Amazon EC2 or an on-premises compute platform. It is installed and configured on the target instances. It accepts and executes requests on behalf of CodeDeploy.
        
- **Deployment configuration** 
    
    - A _deployment configuration_ is a set of deployment rules and deployment success and failure conditions used by AWS CodeDeploy during a deployment. For an Amazon EC2 compute platform, it specifies the number or percentage of instances that must remain available during deployment. It also specifies if an instance in the deployment group is briefly taken offline and updated with the latest code revision, or if a new instance replaces the instances in the deployment group.

## Infrastructure management services

Handling your infrastructure as infrastructure-as-code (IaC) increases the team's agility and efficiency, makes the infrastructure more robust, and decreases overall cost. This is because with IaC, deploying and updating your infrastructure is consistent and repeatable.

- **AWS CloudFormation** is an infrastructure-as-code service. Using templates, you can describe your needed resources and their dependencies, so you can quickly and consistently provision them and manage them throughout their lifecycles. Create, update, and delete an entire stack as a single unit, as often as you need to, instead of managing resources individually. You can manage and provision stacks across multiple AWS accounts and AWS Regions.
- **AWS OpsWorks** is a configuration management service. It provides managed instances of Chef and Puppet automation platforms and helps you automate how servers are configured, deployed, and managed across your Amazon EC2 instances or on-premises compute environments.

## Containers and serverless services

AWS offers a number of services that support development efforts and enhance your pipeline, including:

- **AWS Lambda** is a serverless compute service that lets you write code and run it when it gets invoked. With Lambda functions, you can customize your CI/CD pipeline. For example, you can approve release actions, halt the release flow, and control traffic flow during deployments, and more. 
- **Amazon Elastic Container service** **(Amazon ECS)** is a highly scalable, high performance container management service that supports Docker containers. It enables you to easily run applications on a managed cluster of Amazon Elastic Compute Cloud (Amazon EC2) instances. ECS eliminates the need for you to install, operate, and scale container management software.

## Monitoring services

Monitoring is a key part of DevOps. It helps you to be proactive in preventing challenges before they occur. Here are a few monitoring services you should know.

- **A****WS X-Ray** is a distributed tracing system. It helps you analyze and debug your distributed applications, such as those built using microservices, or serverless architecture. It provides an end-to-end view of requests as they travel through your application, and shows a map of your application's underlying components.
- **Amazon CloudWatch** is a monitoring and management service that provides data and actionable insights for AWS, hybrid, and on-premises applications and infrastructure resources. With CloudWatch, you can track resources and application performance, collect and monitor log files, troubleshoot, and set alarm notifications.
    
- **AWS Config** is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources. AWS Config continuously monitors and records your AWS resource configurations. It enables you to automate the evaluation of recorded configurations against desired configurations. 
    
- **AWS CloudTrail** is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account. With CloudTrail, you can log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. CloudTrail provides event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. This event history simplifies security analysis, resource change tracking, and troubleshooting. In addition, you can use CloudTrail to detect unusual activity in your AWS accounts. These capabilities help simplify operational analysis and troubleshooting.