### Services used in this lab

#### AWS CloudFormation

AWS CloudFormation gives developers and systems administrators an easy way to create and manage a collection of related AWS resources, provisioning and updating them in an orderly and predictable fashion.

You can use AWS CloudFormation’s sample templates or create your own _templates_ to describe the AWS resources, and any associated dependencies or runtime parameters, required to run your application. You don’t need to figure out the order for provisioning AWS services or the subtleties of making those dependencies work. CloudFormation takes care of this for you. After the AWS resources are deployed, you can modify and update them in a controlled and predictable way, in effect applying version control to your AWS infrastructure the same way you do with your software.

You can deploy and update a template and its associated collection of resources (called a _stack_) by using the AWS Management Console, AWS Command Line Interface, or APIs. CloudFormation is available at no additional charge, and you pay only for the AWS resources needed to run your applications.

#### Amazon Virtual Private Cloud (VPC)

Amazon Virtual Private Cloud (Amazon VPC) lets you provision a logically isolated section of the AWS cloud where you can launch resources within a virtual network. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways.

You can easily customize the network configuration for your virtual private cloud. For example, you can create a _public-facing subnet_ for your web servers that has access to the Internet and place your backend systems such as databases or application servers in a _private-facing subnet_ with no Internet access. You can leverage multiple layers of security, including security groups and network access control lists, to help control access to Amazon EC2 instances in each subnet.

### AWS services not used in this lab

AWS service capabilities used in this lab are limited to what the lab requires. Expect errors when accessing other services or performing actions beyond those provided in this lab guide.

---

## Task 1: Deploy a Stack using AWS CloudFormation

In this task, you deploy a pre-defined CloudFormation template to build an Amazon VPC.

3. Download the [vpc-1.yaml](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/spl-15/v4.1.15.prod-6085947e/scripts/vpc-1.yaml) CloudFormation template to your computer.

You examine the contents of the template in the next task.

4. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    CloudFormation
    
    .

 **Caution:** One or more stacks may already appear in the list. They were created for you when the lab was started. You create an additional stack.

5. Choose Create stack .
    
6. Choose With new resources (standard).
    
7. On the **Create stack** page, make the following selections:
    

- In the **Prerequisite - Prepare template** section, choose **Choose an existing template**.
- In the **Specify template** section, choose **Upload a template file** and then choose  Choose file .

8. Browse to and select the **vpc-1.yaml** template you just downloaded.
    
9. Choose Next.
    
10. On the **Specify stack details** page, locate the **Stack name** textbox and enter 
    
    Lab
    
    .
    
11. Choose Next.
    

 **Note:** The **Configure stack options** page allows you to specify **Tags**, **Permissions**, **Stack failure options**, and **Advanced options**.

12. On the **Configure stack options** page, navigate to the bottom of the screen and choose Next.
    
13. On the **Review and create** page, review the selections made and choose Submit.
    

The new stack status is  CREATE_IN_PROGRESS until the resources creation is complete.

While you are waiting, look at the **Events** tab to view the operations that are tracked by CloudFormation.

14. Choose the **Stack info** tab.
    
15.  **Refresh:** Choose the  refresh button every 30 seconds until the stack status changes to  CREATE_COMPLETE.
    

When the stack status is  CREATE_COMPLETE, it means that the resources creation is complete.

16. Choose the **Resources** tab.

 **Refresh:** A list of resources is displayed. These resources are explained in the next task. You may need to refresh the screen to see the resources.

 **Task complete:** You have successfully deployed a pre-defined CloudFormation template to build an Amazon VPC.

---

## Task 2: Examine the Amazon VPC

In this task, you examine the Amazon VPC resources that were created by CloudFormation template and review the code snippets that created those resources.

The following resources were created by CloudFormation:

- An Amazon VPC
- An Internet Gateway
- Two Subnets
- Two Route Tables

![vpc-1.png](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/spl-15/v4.1.15.prod-6085947e/images/vpc-1.png)

_Image description: The preceding diagram depicts the Amazon VPC resources that were created by CloudFormation template. The following resources were created by CloudFormation: An Amazon VPC, an internet gateway, two subnets, and two route tables._

All these resources reside within one **Availability Zone**. An Availability Zone is an isolated location within a Region, and consists of one or more data centers.

17. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    VPC
    
    .
    
18. From the left navigation pane, expand the **Virtual private cloud** section and choose **Your VPCs**.
    
19. Select the checkbox next to **Lab VPC**.
    

A VPC is an isolated section of the AWS Cloud that allows resources to communicate with each other and, selectively, with the Internet. When deploying resources such as Amazon EC2 instances, you must select the VPC in which the instance is launched.

The **Details** tab displays the **IPv4 CIDR**, which is a range of IP addresses assigned to the VPC. This VPC has a CIDR of _10.0.0.0/16_, which means it contains all IP addresses that start with _10.0.x.x_.

 **File contents:** The following code from the CloudFormation template created this VPC:

```
AWSTemplateFormatVersion: 2010-09-09
Description: Deploy a VPC

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      Tags:
      - Key: Name
        Value: Lab VPC
```

The **Type** parameter in the above code declares the type of resources being created by CloudFormation. The **Properties** section then specifies more information about the resource to create. In this case, it defines:

- **CidrBlock:** The IP address range associated with the VPC.
- **EnableDnsHostnames:** Configures the VPC to associate DNS names with Amazon EC2 instances.
- **Tags:** Adds a friendly name to the resource.

Each type of resource has a different set of properties that can be used.

20. From the left navigation pane, expand the **Virtual private cloud** section and choose **Internet gateways**.

An **Internet gateway** is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the Internet. It therefore imposes no availability risks or bandwidth constraints on your network traffic.

An Internet gateway serves two purposes: to provide a target in your VPC route tables for Internet-routable traffic, and to perform network address translation (NAT) for instances that have been assigned public IPv4 addresses.

 **File contents:** The following code from the CloudFormation template created this Internet Gateway:

  ```
  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
      - Key: Name
        Value: Lab Internet Gateway
```

On the **Internet gateways** page, the **Lab Internet Gateway** is showing that it is  Attached to the VPC.

 **File contents:** This was done with the following code in the CloudFormation template:

  ```
  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
```

A VPC Gateway Attachment creates a relationship between a VPC and a gateway, such as this Internet Gateway.

Notice that the template refers to other elements in the template with the _!Ref_ keyword, followed by the name of the other resource. This makes it easy to build resources that link to each other simply by referencing their name.

21. From the left navigation pane, expand the **Virtual private cloud** section and choose **Subnets**.
    
22. On the **Subnets** page, search for 
    
    Lab VPC
    
     using the _Find resources by attribute or tag_ search box and press enter.
    

Two subnets appear:

- **Public Subnet 1** is connected to the Internet via the Internet Gateway and can be used by resources that need to be publicly accessible.
- **Private Subnet 1** is _not_ connected to the Internet. Any resources in this subnet cannot be reached from the Internet, thereby providing additional security around these resources.

 **File contents:** The following code from the CloudFormation template created these subnets:

  ```
  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.0.0/24
      AvailabilityZone: !Select
        - '0'
        - !GetAZs ''
      Tags:
        - Key: Name
          Value: Public Subnet 1

  PrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select
        - '0'
        - !GetAZs ''
      Tags:
        - Key: Name
          Value: Private Subnet 1
```

The properties are:

- **VpcId** refers to the VPC that contains the subnet.
- **CidrBlock** is the range of IP address assigned to the subnet.
- **AvailabilityZone** defines which physical location within the Region should contain the subnet.

 **Note:** Note that the Availability Zone is using a function called **!Select** and a function called **!GetAZs**. The code is retrieving a list of Availability Zones within the region and is referencing the first element from the list. In this manner, the template can be used in any region because it retrieves the list of Availability Zones at runtime rather than having the Availability Zones hard-coded in the template.

23. From the left navigation pane, expand the **Virtual private cloud** section and choose **Route tables**.
    
24. Select the checkbox next to **Public Route Table**.
    
25. Choose the **Routes** tab in the lower half of the window.
    

Route Tables are used to direct (or _route_) traffic in and out of subnets. The configuration for this route table is:

- For traffic within the VPC (10.0.0.0/16), route the traffic locally.
- For traffic going to the Internet (0.0.0.0/0), route the traffic to the Internet Gateway (indicated by _igw-_).

 **File contents:** The following code from the CloudFormation template created the Public Route Table:

  ```
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: Public Route Table
```

There is also similar code for the Private Route Table.

 **File contents:** The following code from the CloudFormation template defines the route to the Internet within the Public Route Table:

  ```
  PublicRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
```

The configuration for the route is:

- **RouteTableId** indicates the Route Table that owns the route.
- **DestinationCidrBlock** defines the IP address range for this routing rule (where 0.0.0.0/0 refers to traffic bound for the Internet).
- **GatewayId** defines where to route the traffic, which in this case is the Internet Gateway that was defined earlier in the template.

This Route is only configured for the Public Route Table, which is what makes it _public_.

26. Choose the **Subnet associations** tab for the pre-selected **Public Route Table**.

The console shows that the **Public Route Table** is _associated_ with **Public Subnet 1**. A Route Table can be associated with multiple subnets, with each association requiring an explicit linkage.

 **File contents:** The following code from the CloudFormation template defines this linkage:

  ```
  PublicSubnetRouteTableAssociation1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet1
      RouteTableId: !Ref PublicRouteTable
```

It declares that **Public Subnet 1** is associated with the **Public Route Table**.

In additional to creating resources, CloudFormation can provide information about the resources that were created.

27. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    CloudFormation
    
    .
    
28. Select the link to the **Lab** stack to view additional information related to this stack.
    
29. Choose the **Outputs** tab.
    

The CloudFormation template is configured to return information about the resources it created:

- **VPC** is the ID of the VPC that was created.
- **AZ1** shows the Availability Zone in which the Subnets were created.

 **File contents:** The following code from the CloudFormation template configured the Outputs:

```
Outputs:
  VPC:
    Description: VPC
    Value: !Ref VPC

  AZ1:
    Description: Availability Zone 1
    Value: !GetAtt
      - PublicSubnet1
      - AvailabilityZone
```

The VPC output is simply a reference to the VPC, which results in the VPC ID being displayed.

 **Note:** The AZ1 output uses the **!GetAtt** function to retrieve an _attribute_ of the resources. In this case, it is retrieving the AvailabilityZone attribute from Public Subnet 1.

 **Task complete:** You have successfully examined the Amazon VPC resources that were created by CloudFormation template and reviewed the code snippets that created those resources.

---

## Task 3: Updating a Stack

Once a CloudFormation stack has been deployed, it is recommended that any changes to the resources should be made through CloudFormation rather than by directly modifying the resources.

In this task, you update the stack with a new CloudFormation template that defines the following resources:

![vpc-2.png](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/spl-15/v4.1.15.prod-6085947e/images/vpc-2.png)

_Image description: The preceding diagram depicts the Amazon VPC resources that were created by CloudFormation template after updating the stack._

Additional public and private subnets have been added in another Availability Zone. This is best practice to ensure that your resources can run in multiple data centers (Availability Zones) to ensure _High Availability_ in case of system failures.

30. Download the updated [vpc-2.yaml](https://us-west-2-tcprod.s3.us-west-2.amazonaws.com/courses/spl-15/v4.1.15.prod-6085947e/scripts/vpc-2.yaml) CloudFormation template to your computer.
    
31. From the CloudFormation Stacks page, select **Lab** stack from the list of stacks if not already selected.
    
32. Choose Update stack.
    
33. Choose **Make a direct update**.
    
34. Choose **Replace existing template**.
    
35. In the **Specify template** section, choose **Upload a template file** and then choose  Choose file .
    
36. Browse to and select the **vpc-2.yaml** template you just downloaded.
    
37. Choose Next.
    
38. On the **Specify stack details** page, choose Next.
    
39. On the **Configure stack options** page, choose Next.
    
40. On the **Review Lab** page, review the selections made and view the list of the changes to be deployed in the **Change set preview** section.
    

The **Change set preview** section shows how resources are updated.

In this case, it indicates that two new _subnets_ are created. In addition, two _route table associations_ are added, to associate these subnets with their appropriate route tables.

41. Choose Submit.

Examine the **Events** tab while the stack is updating.

42. Choose the **Stack info** tab.
    
43.  **Refresh:** Choose the  **refresh** button every 30 seconds to see the updated events until the stack status displays  UPDATE_COMPLETE.
    
44. Choose the **Outputs** tab.
    

An additional Availability Zone is displayed, with a different value to the original Availability Zone.

45. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    VPC
    
    .
    
46. From the left navigation pane, expand the **Virtual private cloud** section and choose **Subnets**.
    
47. On the **Subnets** page, search for 
    
    Lab VPC
    
     using the _Find resources by attribute or tag_ search box and press enter.
    

Four subnets are now displayed. Select each of them and examine their settings in the **Route table** tab.

 **Task complete:** You have successfully updated the stack with a new CloudFormation template and the **Lab VPC** has been updated to support highly available applications.

---

## Task 4: Viewing a Stack in AWS Infrastructure Composer

In this task, you view the template using the AWS Infrastructure Composer.

AWS Infrastructure Composer (formerly known as Application Composer) helps you visually compose and configure modern applications on AWS. Instead of writing code, you can drag and drop different resources to build your application visually.

Infrastructure Composer in CloudFormation console mode is the recommended tool to work with CloudFormation templates visually. This version of Infrastructure Composer that you can access from the CloudFormation console is an improvement from an older tool called AWS CloudFormation Designer. With Infrastructure Composer in CloudFormation console mode, you can drag, drop, configure, and connect a variety of resources, called cards, onto a visual canvas. This visual approach makes it easy to design and edit your application architecture without having to work with templates directly.

48. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    CloudFormation
    
    .
    
49. Select the link to the **Lab** stack to view additional information related to this stack.
    
50. Choose the **Template** tab.
    
51. Choose View in Infrastructure Composer.
    

The top portion of the window provides a graphical overview of the resources that are defined by the template. Dotted lines show the relationship between resources, such as subnets that are associated with VPC.

52. Use the zoom controls to examine the diagram. You can move the diagram by dragging the image.
    
53. Choose any element from the diagram and then choose **Details**.
    

A **Resource properties** pop-up window displays to the right side of the page. Here you see the **Resource configuration** code within the template that defines the resource.

54. On the **Infrastructure Composer** page, choose the **Template** tab.

Here you see the full template that you used to create the **Lab** stack.

55. Choose the toggle button to switch between **YAML** and **JSON**.

 **Note:** Infrastructure Composer can also convert code between JSON and YAML formats.

 **Learn more:** Refer to _Create templates visually with Infrastructure Composer_ in the **Additional resources** section for more information.

 **Task complete:** You have successfully viewed the template using the AWS Infrastructure Composer.

---

## Task 5: Delete the Stack

In this task, you delete the stack, which automatically deletes the VPC and its components.

56. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    CloudFormation
    
    . If your browser displays a prompt asking whether you want to leave the page, choose the option to **Leave**.
    
57. On the **Stacks** page, select the radio button next to **Lab** stack.
    
58. Choose Delete.
    
59. On the **Delete stack?** pop-up window, choose Delete.
    
60.  **Refresh:** Choose the  refresh button every 30 seconds until the stack is deleted.
    

When the stack deletion is completed, it disappears from the list. At the point, the **Lab VPC** is also deleted.

61. At the top of the AWS Management Console, in the search bar, search for and choose 
    
    VPC
    
    .
    
62. From the left navigation pane, expand the **Virtual private cloud** section and choose **Your VPCs**.
    

The **Lab VPC** is no longer listed. In addition, the associated Internet Gateway, Subnets and Route Tables were also deleted.

 **Task complete:** You have successfully deleted the stack, which automatically deleted the VPC and its components.

---

## Knowledge Check

![](data:image/svg+xml,%3csvg%20width='180'%20height='115'%20viewBox='0%200%20180%20115'%20fill='none'%20xmlns='http://www.w3.org/2000/svg'%3e%3cpath%20d='M13.3081%203C13.3081%201.89543%2014.2035%201%2015.3081%201H164.691C165.796%201%20166.691%201.89543%20166.691%203V103.08H13.3081V3Z'%20fill='%23F4F4F4'%20stroke='%23000716'%20stroke-width='2'/%3e%3crect%20x='22.5205'%20y='9.07141'%20width='135.763'%20height='85.7589'%20rx='2'%20fill='%23F2F8FD'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M1%20103.902C1%20103.35%201.44772%20102.902%202%20102.902H178C178.552%20102.902%20179%20103.35%20179%20103.902V109C179%20111.761%20176.761%20114%20174%20114H6C3.23858%20114%201%20111.761%201%20109V103.902Z'%20fill='%23F4F4F4'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M77.4292%20103.743C77.4292%20103.278%2077.8056%20102.902%2078.27%20102.902H102.735C103.2%20102.902%20103.576%20103.278%20103.576%20103.743V103.743C103.576%20106.064%20101.694%20107.946%2099.3722%20107.946H81.6331C79.3113%20107.946%2077.4292%20106.064%2077.4292%20103.743V103.743Z'%20stroke='%23000716'%20stroke-width='2'/%3e%3crect%20x='45'%20y='16.2723'%20width='76.4239'%20height='18.8683'%20rx='2'%20fill='white'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M113.167%2025.9485C113.167%2028.0684%20111.449%2029.7865%20109.33%2029.7865C107.211%2029.7865%20105.493%2028.0684%20105.493%2025.9485C105.493%2023.8286%20107.211%2022.1105%20109.33%2022.1105C111.449%2022.1105%20113.167%2023.8286%20113.167%2025.9485Z'%20fill='%23F4F4F4'%20stroke='%23000716'%20stroke-width='2'/%3e%3cline%20x1='54.189'%20y1='24.9481'%20x2='90.4661'%20y2='24.9481'%20stroke='%23000716'%20stroke-width='2'/%3e%3crect%20x='45'%20y='16'%20width='89'%20height='22.0103'%20rx='2'%20fill='white'%20stroke='%23000716'%20stroke-width='2'/%3e%3cline%20x1='65.3164'%20y1='26.3958'%20x2='98.9474'%20y2='26.3958'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M58.8149%2026.0551C59.4814%2026.4401%2059.4814%2027.4022%2058.8149%2027.7871L53.0954%2031.09C52.4287%2031.475%2051.5953%2030.9938%2051.5953%2030.224L51.5953%2023.6182C51.5953%2022.8484%2052.4287%2022.3673%2053.0954%2022.7523L58.8149%2026.0551Z'%20fill='%23000716'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M124.687%2027.494C124.687%2030.0485%20122.616%2032.1189%20120.063%2032.1189C117.51%2032.1189%20115.439%2030.0485%20115.439%2027.494C115.439%2024.9396%20117.51%2022.8691%20120.063%2022.8691C122.616%2022.8691%20124.687%2024.9396%20124.687%2027.494Z'%20fill='%2389BDEE'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M117.529%2027.4744L119.256%2029.325L122.71%2025.6238'%20stroke='%23000716'%20stroke-width='2'/%3e%3crect%20x='45'%20y='43.6372'%20width='76.4239'%20height='18.8683'%20rx='2'%20fill='white'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M113.167%2053.3134C113.167%2055.4333%20111.449%2057.1514%20109.33%2057.1514C107.211%2057.1514%20105.493%2055.4333%20105.493%2053.3134C105.493%2051.1935%20107.211%2049.4753%20109.33%2049.4753C111.449%2049.4753%20113.167%2051.1935%20113.167%2053.3134Z'%20fill='%23F4F4F4'%20stroke='%23000716'%20stroke-width='2'/%3e%3cline%20x1='54.189'%20y1='52.313'%20x2='90.4661'%20y2='52.313'%20stroke='%23000716'%20stroke-width='2'/%3e%3crect%20x='45'%20y='68.1317'%20width='76.4239'%20height='18.8683'%20rx='2'%20fill='white'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M113.167%2077.8079C113.167%2079.9278%20111.449%2081.6459%20109.33%2081.6459C107.211%2081.6459%20105.493%2079.9278%20105.493%2077.8079C105.493%2075.688%20107.211%2073.9698%20109.33%2073.9698C111.449%2073.9698%20113.167%2075.688%20113.167%2077.8079Z'%20fill='%23F4F4F4'%20stroke='%23000716'%20stroke-width='2'/%3e%3cline%20x1='54.189'%20y1='76.8075'%20x2='90.4661'%20y2='76.8075'%20stroke='%23000716'%20stroke-width='2'/%3e%3cpath%20d='M135.705%2042.5304C135.368%2042.3442%20134.956%2042.5926%20134.963%2042.9774L135.346%2063.4648C135.355%2063.9415%20135.963%2064.1364%20136.247%2063.7538L140.249%2058.3726L144.342%2064.954C144.486%2065.1862%20144.79%2065.2593%20145.024%2065.1182L151.02%2061.5057C151.258%2061.3618%20151.333%2061.0503%20151.186%2060.8134L147.113%2054.2646L153.443%2053.3614C153.918%2053.2937%20154.034%2052.6609%20153.615%2052.4288L135.705%2042.5304Z'%20fill='%23EC7211'%20stroke='%23000716'%20stroke-width='2'%20stroke-linejoin='round'/%3e%3c/svg%3e)

This knowledge check is designed to gauge your understanding of the material covered.

**Why this matters:** Completing this knowledge check will help reinforce your grasp of the content and highlight any areas that may need further review.

Launch knowledge check

---

## Conclusion

You successfully did the following:

- Deployed an AWS CloudFormation template that created an Amazon VPC.
- Examined the components of the template.
- Updated a CloudFormation stack.
- Examined a template with AWS Infrastructure Composer.
- Deleted a CloudFormation stack.

## End lab

Follow these steps to close the console and end your lab.

63. Return to the **AWS Management Console**.
    
64. At the upper-right corner of the page, choose **AWSLabsUser**, and then choose **Sign out**.
    
65. Choose **End Lab** and then confirm that you want to end your lab.