# AWS

Full course: https://www.youtube.com/watch?v=W0JgypX22-Y&list=PLwpoxH5mFQS3TheYrZGl1aYDNxYxneM0T

# Chapter 1: Introduction to AWS Services

## AWS Organisations

AWS allows the definition of a heirarchy of `Organisational Units` (OUs). OUs allow categorisiation of accounts and defining security boundaries using `Service Control Policies`

- OU: Root
  - OU: Payee (SCP1 SCP2)
    - Account: Payee
  - OU: Development (SCP3)
    - Account: Development
    - Account: Testing
  - OU: Production (SCP4)
    - Account: Production
    - Account: Pre-production

Using SCPs you can define what types of actions are allowed by the accounts assigned to a particular OU. e.g. You can ban Developer acccounts access to Machine Learning (ML) services if their main workload is guaranteed to not require them.

`SSO` can be configured via a Master Account from a number of SSO providers, and SSO accounts and account groups can be mapped to AWS OUs.

## AWS Accounts - Identity and Access Management (IAM)

AWS Identity and Access Management enagles you to manage access to AWS services and resources securely. Using IAM you can create and manage aws users and groups and use permissions to allow and deny access to AWS resources.

IAM policies can be applied to users, groups, and a third construct, called a `role`. An IAM role is similar to an IAM user, in that it is an AWS identity with permission policies that determine what the identify can and cannot do in AWS. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it. Also, a role does not have standard long-term credentials such as a password or access keys associated with it. Instead, when you assume a role, it provides you with temporary security credentials for your role session.


## AWS Regions

When you log in to AWS, you have to select the region in which to log in. This determines where AWS resources are created/deployed.

Depending on the application, some considerations for deciding which region to deploy to include:
- Latency
- Compliance and Data Sovereignty
- Service Availability

Additionally, some services may not be available in certain regions. A useful website to get an idea of relative latencies between different regions: https://www.cloudping.co/grid

A `Region` is a physical logcation in the world where AWS has multiple `Availability Zones`. Availability Zones are connected as a mesh network such that if one AZ goes down, others are available. The AZs are also geographically dispersed such that even physical damage to an AZ (e.g. fire/flood) it has limited chance to affect another/all AZs.

An `Availability Zone` itself consists of multiple data centers, each with redunant power, networking, and connectivity housed in seperate facilities.

A `Data Center` is a single building that houses physical machines.

Regions are also connected to other regions via a global network. This can provide options for replication of services accross multiple regions to provide better performance to user bases in different parts of the world.

### AWS Local Zones

AWS Local Zones place computer, storage, database, and other select AWS services closer to end-users.

Use cases include highly-demanding applications that require single-digit millisecond latencies to end-users:

- Media & entertainment Content Creation
- Real-time Multiplayer Gaming
- Machin Learning Inference
- Live video streaming
- AR/VR

Each AWS Local Zone location is an extension of an AWS Region where you can run your latency sensitive applications using AWS services such as Amazon Elastic Compute Cloud, Amazon Virtual Private Cloud, Amazon Elastic Block Store, Amazon File Storage, and Amazon Elastic Load Balancing in geographic proximity to end-users. AWS Local Zones are managed and supported by AWS, bringing you all of the elasticity, scalability, and security benefits of the cloud.

### AWS Outposts

It is a physical rack:

- Industry standard rack - can be 42U rack or only a 1U or 2U rack for smaller applications
- Fully assembled, ready to be rolled into final position
- Installed by AWS simply plugged into power and network
- Centralized reundant power conversion unit and DC distribution system for higher reliability, energy efficiency, easier serviceability
- Redundant active components including top of rack switches and hot spare hosts

It is fully integrated and managed by AWS, but is deployed on premises.

Usecases:

- Latency sensitive applicaitons
  - Need latency less than 1ms
  - Interactive workloads such as AR/VR, design, visualisation
- Local data processing
  - Transcoding, filtering, caching, and alerting applied at edge
  - AWS compute to large datasets that can't be moved easily
- Residency
  - Where regulations dictate that data and infrastructure must reside in specific countries
  - Infosec limitations for companies reluctant to host data in AWS datacenters


Once installed on premise you can manage the Outpost resources using the AWS console/cli.

### AWS Wavelength

A service designed to optimise performance and latency for applications primarily used from mobile devices that can be travelling large distances (e.g. phone apps or IOT devices). AWS Wavelength Zones are AWS infrastructure that has been deployed/integrated with ISP 5G communication hubs to provide low latency response to 5G devices.

## Scope of a Service

### Global Services

These are services that are independent from a specific region. Examples include:
- Billing
- Support
- AWS Organisations
- IAM
- Route 53

### Region Specific Services

These are services that have to be deployed in a specific region. Some examples:
- Simple Storage Service (S3)
- DynamoDB
- AWS Config
- AWS Single Sign On

Within the region, there are additional services that must also be assigned to an Availability Zone. For example:
- EC2 instances
- Elastic Block Storage
- Elastic Network Interface

 
## Questions

1. Why do you need AWS organizations? What benefits does it provide?
2. When to use a single aws account and when to use multiple aws accounts?
3. If you want to grant AWS acess to users in azure ad, which AWS service wll you use to federate azure ad identities into Aws?
4. What are some use cases for aws local zones?
5. What are some use cases for aws outpost?
6. ** Why do you need to deploy solutions on local zones instead of outputs?**
7. Can AWS Wavelenth run on 3G network?
8. Why do you need to deploy solutions on local zones instead of outpost?
9. Can AWS wavelength run on 3G networks?
10. How many AWS regions are available at this point in time? (refer to https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)
11. How AWS availability zones provide redundancy and provide isoluation during natural disasters?



## IAM

### What is it?

- Permission system that regulates access to AWS resources.
- Allows you to assign permissions to groups of users.
- Access auditing using AWS CloudTrail
- Integrates with other Identity tech (e.g Microsoft Active Directory)

### How Does it Work?

4 key concepts - Users, groups, roles, and policies/permissions:

- **Users**: specific individuals that can receive credentials - e.g. personal logins (i.e. username/password, secrect keys
- **Groups**: Collection of users that have common permissions
- **Roles**: Collection of policies (DB read, DB write) - not tied to a specific user, users can be given permission so 'assume' a role, which temporarily grants them the permissions associated with the role.
- **Policies**: low level permission to resources (allow or deny). Example of policy format:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",   // statement id - user defined name of the policy
      "Effect": "Allow",       // Allow / deny
      "Action": [              // Specific actions/permissions this applies to
        "dynamodb:Scan", 
        "dynamodb:Query"
      ],                      // Identifier for the resource to apply the policy to
      "Resource": "arn:aws:dynamodb:us-east-1:398447858632:table/Transactions"
    }
  ]
}
```

- **Trust relationships**: two accounts declare that they 'trust' each other, and it allows the accounts to assume the role of the other account.

### Important Notes

- Protect your **root account** at all costs
- Explicit `Effect: 'Deny'` wins over `Effect: 'Allow'`
- Always setup permissions using the **Least privilege model**


