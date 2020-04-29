---
title: Implementing an Auto Scaling Group and Application Load Balancer
---

In this AWS hands-on lab, we will integrate two powerful AWS services: **Elastic Load Balancers** and **Auto Scaling groups**.

Specifically, we will create an Auto Scaling group of EC2 instances operating as web servers, and we'll configure an Application Load Balancer to load balance between the instances inside that Auto Scaling group.

After everything is set up, we will simulate stress tests on the EC2 instances to confirm the Auto Scaling group works as expected. This experience is good practice for building highly available and cost-efficient applications on AWS.

### Learning Objectives

* Create an Application Load Balancer
* Create a Launch Template
* Create an Auto Scaling Group
* Test Horizontal Scaling

---

![Lab Diagram](/images/AWS_Certified_Solutions_Architect/lab_diagram_ASGandALB.png)

### Create an Application Load Balancer

Create an Application Load Balancer to load balance between EC2 instances you will create later on inside your Auto Scaling group:

1.  Navigate to the EC2 portion of the console.
2.  Click on the Load Balancers section under **Load Balancing**.
3.  Press the Create button under the **Application Load Balancer**.
4.  Name your load balancer "LABALB", leave the ALB set to internet facing, and set the IP address type as ipv4.
5.  Select the VPC, and add the `us-east-1a` and `us-east-1b` AZs to your ALB (*not* the `us-east-1c` AZ).
6.  Create a new security group for your ALB, and use "ALBSG" for the name and description. Configure rules ensuring HTTP is allowed from 0.0.0.0/0 and ::/0 (IPV6).
7.  Configure a target group for your ALB, naming it ALBTG.
8.  Expand *Advanced health check settings*, and reduce the healthy threshold check down to 2.
9.  Proceed to create your ALB.

![Application Load Balancer](/images/AWS_Certified_Solutions_Architect/Lab-ApplicationLoadBalancer.jpg)

### Create a Launch Template

Create an SSH key pair that the EC2 instances will use to control access:

1.  Navigate to EC2 > Network & Security > Key Pairs.
2.  Click Create Key Pair.
3.  Call it ALBASG, and download the file to your local machine.

Create a security group for EC2 instances:

1.  Navigate to EC2 > Network & Security > Security Groups.
2.  Click Create Security Group.
3.  The name and description are ec2web.
4.  Set the VPC to the lab VPC.
5.  Add a rule allowing SSH from 0.0.0.0/0 and ::/0 (IPV6).
6.  Add a rule allowing HTTP from the Security Group ID of the ALB.
7.  Create the security group.

Create a launch template that will be used by the Auto Scaling group:

1.  Navigate to EC2 > Instances > Launch Templates.
2.  Create a new template, and call it "LABLT" for the name and description.
3.  The *Source Template* is none.
4.  Search for AMI, use the `Quick Start` lab catalog, and pick the Amazon Linux 2 AMI (64-bit x86).
5.  Set the instance type as t3.micro (not T3a.micro).
6.  Select the key pair created earlier.
7.  Network type is VPC.
8.  Select the ec2web security group created earlier.
9.  Storage should automatically be populated with a volume, so leave that as default and don't add anything to the network section.
10. Expand *Advanced Details*, and paste the user data for this lesson in the box.
11. Click Create Launch Template.
12. Click Close.

### Create an Auto Scaling Group

1.  EC2 > Auto Scaling > Auto Scaling Groups
2.  Click Create Auto Scaling group.
3.  Select Launch Template, and choose the template you just created. NOTE: If you receive the message `No default VPC found`, you can ignore it as we are selecting our VPC below.
4.  Call the group "LABASG".
5.  Start with two instances.
6.  Pick the VPC from the lab environment, and select `us-east-1a` and `us-east-1b` as subnets.
7.  Click Next: Configure Scaling Policies.
8.  For now, keep the group at the initial size.
9.  Click Next: Configure Notifications > Next: Configure Tags > Review > Create Auto Scaling group > Close.

Note: Make sure the load balancer is ready at this point.

#### Enable Group Metrics Collection

1.  Click the Monitoring tab of the ASG.
2.  Click Enable Group Metrics Collection.

#### Edit the ASG to Allow Scaling and Use the ALB

1.  Click Actions > Edit
2.  Set *Max instances* as 6**, click **Target groups, and pick the target group of the ALB.
3.  Click Save.

#### Configure Auto Scaling Group Scaling Policies

1.  Select the Scaling Policies tab of the ASG.
2.  Click Add policy.
3.  Click Create a simple scaling policy.
4.  Name it SCALEOUT, set it to *Take the action* to Add 1 instances, *And then wait* 300 seconds before allowing another scaling activity.
5.  Click Create new alarm, and uncheck notification.
6.  Average CPU Utilization is >= 40 percent for 1 period of 5 minutes. Call it HIGHCPU.
7.  Click Create alarm and Close.
8.  Click Create.
9.  Click Add policy again.
10. Click Create a simple scaling policy.
11. Name it SCALEIN, set it to *Take the action* to Remove 1 instances, *And then wait* 300 seconds before allowing another scaling activity.
12. Click Create new alarm, and uncheck notification.
13. Average CPU utilization is <= 20 percent for 1 period of 5 minutes. Call it LOWCPU.
14. Click Create alarm > Close > Create.

### Test Horizontal Scaling

1.  Connect to one of the EC2 instances.
2.  Install the stress test application:

    ```
    sudo amazon-linux-extras install epel -y
    sudo yum install -y stress
    ```

3.  Run the stress test on the EC2 instance:

    ```
    stress --cpu 2 --timeout 300
    ```

    (Optionally, use `3000` for timeout.)

4.  After a few minutes, watch the number of instances increase.


![Implement Auto Scaling](/images/AWS_Certified_Solutions_Architect/Lab-AutoScaling.jpg)



