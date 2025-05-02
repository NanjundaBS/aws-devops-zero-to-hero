**What are AWS CloudFormation Templates (CFT)?**

AWS CloudFormation Templates (CFT) are templates used to create infrastructure on AWS. They are templates that help in the **cloud formation of AWS**, which includes creating, managing, and updating the cloud infrastructure on AWS.

**Infrastructure as Code (IaC) and CFT**

CFT implements the principles of Infrastructure as Code (IaC). IaC is a concept where you write code to create infrastructure, in contrast to writing code to create applications.

Tools that are considered IaC tools should act as a **middleman** between the user and one or multiple cloud providers. In the case of CFT, it acts as a middleman between the user and AWS, as it only supports the AWS cloud provider. Users submit templates (like YAML or JSON files) to the IaC tool. The tool then takes this input and converts the template into a language that the cloud provider understands, usually API calls. CFT does this by taking YAML or JSON templates submitted by the user and converting them into AWS API calls.

A key aspect of IaC tools, including CFT, is that their templates should be **declarative and versioned** in nature.
*   **Versioned:** This means the templates can be stored in repositories like Git or S3 buckets, allowing you to trace changes and go back to previous versions.
*   **Declarative:** A declarative template states "what you see is what you have". By looking at a declarative template, you should be able to directly understand the resources that will be available on the AWS infrastructure. It should be simple enough for anyone to perform code review or auditing. Looking at a CFT template designed to create resources like VPC, route tables, load balancer, and EC2, you should easily understand that these resources will be created when the template is executed.

**CFT vs. AWS CLI**

AWS CLI can also be used to manage and create infrastructure on AWS by passing parameters. However, AWS CLI does *not* implement the principles of IaC, whereas CFT does.

You would typically use AWS CLI for **quick or short actions** or simple scripts, such as getting a list of S3 buckets. Writing and submitting a template for such a quick task using CFT would take more time.

You would use CFT when you want to **create actual resources** like two or three resources, or when you want to create huge stacks (collections of resources).

**Template Formats: YAML vs. JSON**

AWS CloudFormation Templates support both JSON and YAML formats. The source recommends using **YAML** when starting new with CFT because it is widely used in the DevOps world (e.g., Kubernetes, Ansible).
Advantages of YAML include:
*   It supports **commenting**, which is important for team collaboration to help others understand the code. Json does not support commenting.
*   It is generally considered **more readable** because it depends on indentation rather than curly braces and brackets, similar to Python.
*   It is described as **less complex** than JSON.

While YAML is preferred and commonly used, teams can use JSON if they have expertise in it.

**Components of a CFT Template**

A CFT template has a specific structure and consists of different components. Not all components are mandatory. The **only mandatory field** in a cloud formation template is the **resources** section. Even if you don't include any other sections, a template with just the resources section will work.

Here are the basic components discussed:

*   **AWS Template Format Version:** A standard, unchanging version (last changed in 2010).
*   **Description:** A section to describe the purpose of the template, which is helpful for others (or yourself later) to quickly understand it.
*   **Metadata:** Information like the author, team, or project the template is for.
*   **Parameters:** Used to pass variables to the CFT during runtime. This allows the same template to be used for different configurations (e.g., varying image ID or instance type) without modifying the template itself.
*   **Rules:** Used to validate parameters submitted by the user. For example, you can define rules to ensure specific naming conventions are followed or that instance types are within a permitted range (e.g., only T2 micro or T2 medium). If rules are not met, the template request can be rejected.
*   **Mappings:** Used for assigning parameters to variables within the template.
*   **Conditions:** Used to define whether certain resources are created or whether the template executes based on specific conditions. For instance, a condition could ensure a template only runs in Dev and Staging environments, not Production.
*   **Transform:** Mentioned as a component but requires understanding serverless compute services and is not covered in detail.
*   **Resources:** This is the **mandatory section** where you define the AWS resources you want to create, such as EC2 instances, S3 buckets, etc.. You list each resource with a logical name (which can be anything) and its actual type (e.g., `AWS::EC2::Instance`, `AWS::S3::Bucket`), along with its specific configuration properties. You can define multiple resources within this section.
*   **Outputs:** Used to define what specific information you want as output after the template is executed, such as the public IP of a created EC2 instance or the ARN of an S3 bucket.

**Stacks in CFT**

In AWS CFT, a **stack** is the entity that implements the templates. When you write a template, you submit it to a stack. The stack then uses the CloudFormation service to convert your template request into API calls to create the resources. You can create a stack through the AWS UI, CLI, or other tools like Jenkins. When creating a stack via the UI, you prepare or upload your template file.

**Key Features: Drift Detection**

One good feature of CloudFormation is **drift detection**. This feature helps identify if someone has manually modified an AWS resource that was originally created through a CloudFormation template. For example, if a template enabled versioning on an S3 bucket, but someone later manually disabled it, drift detection can identify this unintentional or unwanted change. The status of the stack will show as "drifted," and you can view details to see exactly what configuration change occurred. This is very important for managing many resources, as it helps ensure that your infrastructure configuration matches the configuration defined in your templates.

**Using CFT and Documentation**

AWS provides comprehensive official documentation for CloudFormation, which includes theory, examples, and details about template anatomy. This documentation is highly recommended for learning and using CFT. You can find template references for specific AWS resources and their properties.

The AWS UI also offers a **CloudFormation Designer**, which is a visual tool where you can drag and drop services (like S3 bucket) onto a canvas, and it will generate the corresponding JSON or YAML template code. This can be helpful for beginners.

For writing templates more efficiently, especially in Visual Studio Code, the source recommends two plugins:
1.  **YAML plugin (by Red Hat):** Helps with YAML syntax and indentation.
2.  **AWS Toolkit:** Provides assistance and options specific to AWS services when writing templates, potentially reducing the need to constantly refer to documentation.

**CFT vs. Terraform**

The comparison between CFT and Terraform is often asked in interviews.
*   **CFT** is primarily used when your organization is **dedicated only to AWS**.
*   **Terraform** is typically preferred in **hybrid or multi-cloud environments** (e.g., AWS and Azure, or on-premises and AWS) because it supports multiple cloud providers.

While CFT is valuable if your organization is exclusively on AWS and has no plans to switch, the source suggests that it is generally **better to learn Terraform** because it is currently more widely used and seen as a tool for the future in the market, especially as organizations increasingly adopt multi-cloud strategies. However, if your current role or desired role is specifically focused on an AWS-only environment, knowing CFT is important.
