# AWS

Full course: https://www.youtube.com/watch?v=W0JgypX22-Y&list=PLwpoxH5mFQS3TheYrZGl1aYDNxYxneM0T

## Questions

1. Why do you need AWS organizations? What benefits does it provide?
2. When to use a single aws account and when to use multiple aws accounts?
3. If you want to grant AWS acess to users in azure ad, which AWS service wll you use to federate azure ad identities into Aws?
4. What are some use cases for aws local zones?
5. What are some use cases for aws outpost?
6. Why do you need to deploy solutions on local zones instead of outputs?
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


