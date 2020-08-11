# My SAP-C01 Notes
> These are my personal notes from AWS Documentation.

## Table of Contents

- [IAM](#IAM)
- [Security](#Security)
- [Compute-and-LB](#Compute-and-LB)
- [Storage](#Storage)
- [Cache](#Cache)
- [Databases](#Databases)
- [Service-Communication](#Service-Communication)
- [Data-Engineering](#Data-Engineering)
- [Monitoring](#Monitoring)
- [Instance-Mgmt](#Instance-Mgmt)
- [Cost-Control](#Cost-Control)
- [Migration](#Migration)
- [VPC](#VPC)
- [Other](#Other)

---

## IAM

#### AWS STS
AWS Security Token Service (AWS STS) is a web service that enables you to request temporary, limited-privilege credentials for AWS Identity and Access Management (IAM) users or for users that you authenticate (federated users).
  - is a global service and all AWS STS requests go to a single endpoint at https://sts.amazonaws.com
  - STS recommends that you provide both the Region and endpoint when you make calls to a Regional endpoint
  - STS supports AWS CloudTrail
  - If you activate AWS STS endpoints in Regions other than the default global endpoint, then you must also turn on CloudTrail logging in those Regions.
  - When you activate STS endpoints for a Region, AWS STS can issue temporary credentials to users and roles in your account that make an AWS STS request. Those credentials can then be used in any Region that is enabled by default or is manually enabled. You must activate the Region in the account where the temporary credentials are generated. It does not matter whether a user is signed into the same account or a different account when they make the request.

Using endpoints allows you to - 
- Reduce latency – By making your AWS STS calls to an endpoint that is geographically closer to your services and applications, you can access AWS STS services with lower latency and better response times.
- Build in redundancy – You can add code to your application that switches your AWS STS API calls to a different Region. This ensures that if the first Region stops responding, your application continues to operate. This redundancy is not automatic; you must build the functionality into your code.
- Increase session token validity – Session tokens from Regional AWS STS endpoints are valid in all AWS Regions. Session tokens from the global STS endpoint are valid only in AWS Regions that are enabled by default. 

STS in your VPC - 
- If you use Amazon Virtual Private Cloud (Amazon VPC) to host your AWS resources, you can establish a private connection between your VPC and AWS STS. You can use this connection to enable AWS STS to communicate with your resources on your VPC without going through the public internet.
- To connect your VPC to AWS STS, you define an interface VPC endpoint for AWS STS
- This endpoint provides reliable, scalable connectivity to AWS STS without requiring an internet gateway, network address translation (NAT) instance, or VPN connection
- Interface VPC endpoints are powered by AWS PrivateLink

#### Federation
Web Identity Federation
  - strongly recommend that you do not embed or distribute long-term AWS credentials with apps that a user downloads to a device
  - Instead, build your app so that it requests temporary AWS security credentials dynamically when needed using web identity federation
  - users of your app can sign in using a well-known external identity provider (IdP), such as Login with Amazon, Facebook, Google, or any other OpenID Connect (OIDC)-compatible IdP
  - for most scenarios, use Cognito
  
Cognito
  - The preferred way to use web identity federation is to use Amazon Cognito
  - Your app users can sign in either directly through a user pool, or federate through a third-party identity provider
  - use Amazon Cognito when you need to create custom registration fields and store that metadata in your user directory. This fully managed service scales to support hundreds of millions of users.

![cognito](https://docs.aws.amazon.com/IAM/latest/UserGuide/images/mobile-app-web-identity-federation.diagram.png) 

AWS Directory Services
  - AWS Directory Service provides multiple ways to use Microsoft Active Directory (AD) with other AWS services.
  - provides multiple directory choices for customers who want to use existing Microsoft AD or Lightweight Directory Access Protocol (LDAP)–aware applications

- Select AWS Directory Service for Microsoft Active Directory (Standard Edition or Enterprise Edition) if you need an actual Microsoft Active Directory in the AWS Cloud that supports Active Directory–aware workloads, or AWS applications and services such as Amazon WorkSpaces and Amazon QuickSight, or you need LDAP support for Linux applications.
- Use AD Connector if you only need to allow your on-premises users to log in to AWS applications and services with their Active Directory credentials. You can also use AD Connector to join Amazon EC2 instances to your existing Active Directory domain.
- Use Simple AD if you need a low-scale, low-cost directory with basic Active Directory compatibility that supports Samba 4–compatible applications, or you need LDAP compatibility for LDAP-aware applications.
- Use Amazon Cognito if you develop high-scale SaaS applications and need a scalable directory to manage and authenticate your subscribers and that works with social media identities.

#### AWS Organizations
AWS Organizations is an account management service that enables you to consolidate multiple AWS accounts into an organization that you create and centrally manage.
  - can perform account management activities at scale by consolidating multiple AWS accounts into a single organization
  - You can use trusted access to enable an AWS service that you specify, called the trusted service, to perform tasks in your organization and its accounts on your behalf.
  - Trusted access requires permissions for two services: AWS Organizations and the trusted service

![awsorgs](https://docs.aws.amazon.com/organizations/latest/userguide/images/BasicOrganization.png)

  - Policies in AWS Organizations enable you to apply additional types of management to the AWS accounts in your organization. You can use policies when all features are enabled in your organization.
  - Authorization policies help you to centrally manage the security of the AWS accounts in your organization -- Service control policies (SCPs) offer central control over the maximum available permissions for all of the accounts in your organization.
  - Management policies enable you to centrally configure and manage AWS services and their features -- AI services opt-out policies, backup policies, and tag policies.

You can attach policies to organization entities (organization root, organizational unit (OU), or account) in your organization:
1. When you attach a policy to the organization root, all OUs and accounts in the organization inherit that policy.
2. When you attach a policy to a specific OU, accounts that are directly under that OU or any child OU inherit the policy.
3. When you attach a policy to a specific account, it affects only that account.

#### AWS Resource Access Manager (RAM)
AWS Resource Access Manager (AWS RAM) lets you share your resources with any AWS account or through AWS Organizations. If you have multiple AWS accounts, you can create resources centrally and use AWS RAM to share those resources with other accounts.

RAM offers
- Reduces operational overhead—Create resources centrally and use AWS RAM to share those resources with other accounts. This eliminates the need to provision duplicate resources in every account, which reduces operational overhead.
- Provides security and consistency—Govern consumption of shared resources using existing policies and permissions, to achieve security and control. AWS RAM offers a consistent experience for sharing different types of AWS resources.
- Provides visibility and auditability—View usage details for shared resources through integration with Amazon CloudWatch and AWS CloudTrail. AWS RAM provides comprehensive visibility into shared resources and accounts.

Services that are shareable
1. AWS App Mesh
2. Amazon Aurora
3. AWS CodeBuild
4. Amazon EC2
5. Amazon EC2 Image Builder
6. AWS Glue
7. AWS License Manager
8. AWS Resource Groups
9. Amazon Route 53

When you create a resource share, AWS RAM automatically attaches the default permission for each associated resource type to the resource share. For example, if you create a resource share and associate a subnet and a Capacity Reservation, AWS RAM automatically attaches the subnet and Capacity Reservation permissions to the resource share.

#### AWS SSO
AWS Single Sign-On is a cloud-based single sign-on (SSO) service that makes it easy to centrally manage SSO access to all of your AWS accounts and cloud applications.
  - includes a user portal where your end-users can find and access all their assigned AWS accounts
  - Integration with AWS Organizations
  - SSO access to your AWS accounts and cloud applications - SAML 2.0–based applications
  - Create and manage users and groups in AWS SSO
  - Leverage your existing corporate identities - is integrated with Microsoft AD through the AWS Directory Service
  - Compatible with commonly used cloud applications - Salesforce, Box, and Office 365
  - Easy to set up and monitor usage
  - By default, when a user signs in to the user portal, they sign in with their email address and password (the first factor). This is the default authentication mechanism used in AWS SSO. But when multi-factor authentication (MFA) is enabled, users enter an MFA code (the second factor) that is generated by an application on their phone. 

AWS SSO is integrated with AWS CloudTrail = use it!

## Security

#### Cloudtrail
AWS CloudTrail is an AWS service that helps you enable governance, compliance, and operational and risk auditing of your AWS account. Actions taken by a user, role, or an AWS service are recorded as events in CloudTrail. Events include actions taken in the AWS Management Console, AWS Command Line Interface, and AWS SDKs and APIs.
  - By default, trails log management events, but not data events (Data events are disabled by default)
  - CloudTrail Insights events capture unusual activity
  - Insights events are logged only when CloudTrail detects changes in your account's API usage that differ significantly from the account's typical usage patterns
  - Example = Your account typically logs 20 calls per minute to the Amazon EC2 AuthorizeSecurityGroupIngress API, but your account starts to log zero calls to AuthorizeSecurityGroupIngress

Best practices
1. Create a trail and apply that trail to all AWS Regions
2. Enable CloudTrail log file integrity
3. Integrate with Amazon CloudWatch Logs
4. Log to a dedicated and centralized Amazon S3 bucket
5. Use server-side encryption with AWS KMS managed keys
6. Implement least privilege access and enable MFA Delete
7. Limit access to the AWSCloudTrailFullAccess policy

Notifications
- You can be notified when CloudTrail publishes new log files to your Amazon S3 bucket. You manage notifications using SNS.
- CloudTrail sends a notification when log files are written to the Amazon S3 bucket

#### KMS
AWS Key Management Service (AWS KMS) is a managed service that makes it easy for you to create and control customer master keys (CMKs)
- The primary way to manage access to your AWS KMS CMKs is with policies
- Key policies are the primary way to control access to customer master keys (CMKs) in AWS KMS. They are not the only way to control access, but you cannot control access without them.

AWS KMS supports symmetric and asymmetric CMKs.
- Symmetric CMK: Represents a single 256-bit secret encryption key that never leaves AWS KMS unencrypted. To use your symmetric CMK, you must call AWS KMS.
- Asymmetric CMK: Represents a mathematically related public key and private key pair that you can use for encryption and decryption or signing and verification, but not both. The private key never leaves AWS KMS unencrypted. You can use the public key within AWS KMS by calling the AWS KMS API operations, or download the public key and use it outside of AWS KMS.

Valid key usage for CMK types
| CMK Type | Encrypt and decrypt | Sign and verify |
| --------|---------|-------|
| Symmetric CMKs  | Yes | No |
| Asymmetric CMKs with RSA key pairs | Yes | Yes |
| Asymmetric CMKs with ECC key pairs | No | Yes |

Rotating Keys
Cryptographic best practices discourage extensive reuse of encryption keys. To create new cryptographic material for your AWS Key Management Service (AWS KMS) customer master keys (CMKs), you can create new CMKs, and then change your applications or aliases to use the new CMKs. When you enable automatic key rotation for a customer managed CMK, AWS KMS generates new cryptographic material for the CMK every year. AWS KMS also saves the CMK's older cryptographic material in perpetuity so it can be used to decrypt data that it encrypted. AWS KMS does not delete any rotated key material until you delete the CMK.

Custom Key Store
AWS KMS supports custom key stores backed by AWS CloudHSM clusters. When you create an AWS KMS customer master key (CMK) in a custom key store, AWS KMS generates and stores non-extractable key material for the CMK in an AWS CloudHSM cluster that you own and manage. When you use a CMK in a custom key store, the cryptographic operations are performed in the HSMs in the cluster. This feature combines the convenience and widespread integration of AWS KMS with the added control of an AWS CloudHSM cluster in your AWS account.

Use Key Store if you have the following requirements
- Key material cannot be stored in a shared environment.
- Key material must be backed up in multiple AWS Regions.
- Key material must be subject to a secondary, independent audit path.
- The HSMs that generate and store key material must be certified at FIPS 140-2 Level 3.

#### Parameter Store
AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, Amazon Machine Image (AMI) IDs, and license codes as parameter values.
- Reference AWS Secrets Manager secrets by using Parameter Store parameters
- Use Parameter Store parameters with other Systems Manager capabilities and AWS services to retrieve secrets and configuration data from a central store

Parameter Store can integrate with the following:
- AWS Key Management Service (AWS KMS)
- Amazon Simple Notification Service (Amazon SNS)
- Amazon CloudWatch
- AWS CloudTrail
- AWS Secrets Manager (can retrieve Secrets Manager secrets when using other AWS services that already support references to Parameter Store parameters)

Do not store sensitive data in a String or StringList parameter. For all sensitive data that must remain encrypted, use only the SecureString parameter type.

SecureString
The SecureString parameter type can be used for textual data that you want to encrypt, such as passwords, application secrets, confidential configuration data, or any other types of data you need to protect. SecureString data is encrypted and decrypted using a AWS Key Management Service (KMS) key. You can use either a default KMS key provided by AWS or create and use your own customer master key (CMK). Use cases for using SecureString:
- You want to use data/parameters across AWS services without exposing the values as plain text in commands, functions, agent logs, or AWS CloudTrail logs.
- You want to control who has access to sensitive data.
- You want to be able to audit when sensitive data is accessed (AWS CloudTrail).
- You want to encrypt your sensitive data and you want to bring your own encryption keys to manage access.

#### Secrets Manager


#### SNI and MITM


#### AWS Certificate Manager


#### CloudHSM


#### S3 Security


#### Network Security


#### AWS Inspector


#### AWS Config


#### AWS Managed Logs


#### AWS GuardDuty








