# Implement secure cloud solutions - Table of contents

- [Implement secure cloud solutions - Table of contents](#implement-secure-cloud-solutions---table-of-contents)
  - [Implement Azure Key Vault](#implement-azure-key-vault)
    - [Authentication](#authentication)
      - [Encryption of data in transit](#encryption-of-data-in-transit)
      - [Azure Key Vault best practices](#azure-key-vault-best-practices)
  - [Implement managed identities](#implement-managed-identities)
    - [System-assigned managed identity](#system-assigned-managed-identity)
    - [User-assigned managed identity](#user-assigned-managed-identity)
    - [Acquire an access token](#acquire-an-access-token)
  - [Implement Azure App Configuration](#implement-azure-app-configuration)
    - [Manage application features](#manage-application-features)
      - [Feature flag repository](#feature-flag-repository)
    - [Secure app configuration data](#secure-app-configuration-data)
      - [Enable customer-managed key capability](#enable-customer-managed-key-capability)

## Implement Azure Key Vault

Azure Key Vault is a cloud service for securely storing and accessing secrets. A secret is anything that you want to tightly control access to, such as API keys, passwords, certificates, or cryptographic keys.

Azure Key Vault helps solve the following problems:

- Secrets Management: Azure Key Vault can be used to Securely store and tightly control access to tokens, passwords, certificates, API keys, and other secrets

- Key Management: Azure Key Vault can also be used as a Key Management solution. Azure Key Vault makes it easy to create and control the encryption keys used to encrypt your data.

- Certificate Management: Azure Key Vault is also a service that lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with Azure and your internal connected resources.

Azure Key Vault has two service tiers:

1. Standard, which encrypts with a software key
2. Premium tier, which includes hardware security module(HSM)-protected keys.

Access to a key vault requires proper authentication and authorization before a caller (user or application) can get access. Authentication is done via Azure Active Directory. Authorization may be done via Azure role-based access control (Azure RBAC) or Key Vault access policy.
You have control over your logs and you may secure them by restricting access and you may also delete logs that you no longer need.

### Authentication

There are three ways to authenticate to Key Vault:

- Managed identities for Azure resources: When you deploy an app on a virtual machine in Azure, you can assign an identity to your virtual machine that has access to Key Vault. You can also assign identities to other Azure resources. The benefit of this approach is that the app or service isn't managing the rotation of the first secret. Azure automatically rotates the service principal client secret associated with the identity. **We recommend this approach as a best practice.
**
- Service principal and certificate: You can use a service principal and an associated certificate that has access to Key Vault. **We don't recommend this approach** because the application owner or developer must rotate the certificate.

- Service principal and secret: Although you can use a service principal and a secret to authenticate to Key Vault, **we don't recommend it**. It's hard to automatically rotate the bootstrap secret that's used to authenticate to Key Vault.

#### Encryption of data in transit

Azure Key Vault enforces Transport Layer Security (TLS) protocol to protect data when it’s traveling between Azure Key Vault and clients. Clients negotiate a TLS connection with Azure Key Vault. TLS provides strong authentication, message privacy, and integrity (enabling detection of message tampering, interception, and forgery), interoperability, algorithm flexibility, and ease of deployment and use.

#### Azure Key Vault best practices

Use separate key vaults: Recommended to use a vault per application per environment (Development, Pre-Production and Production). This helps you not share secrets across environments and also reduces the threat in case of a breach.

## Implement managed identities

Managed identities eliminate the need for developers to manage credentials.
Managed identities provide an identity for applications to use when connecting to resources that support Azure Active Directory (Azure AD) authentication. Applications may use the managed identity to obtain Azure AD tokens.
There are two types of managed identities:

- A system-assigned managed identity is enabled directly on an Azure service instance. When the identity is enabled, Azure creates an identity for the instance in the Azure AD tenant that's trusted by the subscription of the instance. After the identity is created, the credentials are provisioned onto the instance. The lifecycle of a system-assigned identity is directly tied to the Azure service instance that it's enabled on. If the instance is deleted, Azure automatically cleans up the credentials and the identity in Azure AD.
- A user-assigned managed identity is created as a standalone Azure resource. Through a create process, Azure creates an identity in the Azure AD tenant that's trusted by the subscription in use. After the identity is created, the identity can be assigned to one or more Azure service instances. The lifecycle of a user-assigned identity is managed separately from the lifecycle of the Azure service instances to which it's assigned.

You can configure an Azure virtual machine with a managed identity during, or after, the creation of the virtual machine.

### System-assigned managed identity

To create, or enable, an Azure virtual machine with the system-assigned managed identity your account needs the Virtual Machine Contributor role assignment. No additional Azure AD directory role assignments are required.

### User-assigned managed identity

To assign a user-assigned identity to a virtual machine during its creation, your account needs the Virtual Machine Contributor and Managed Identity Operator role assignments. No additional Azure AD directory role assignments are required.
Enabling user-assigned managed identities is a two-step process:

  1. Create the user-assigned identity
  2. Assign the identity to a virtual machine

### Acquire an access token

A client application can request managed identities for Azure resources app-only access token for accessing a given resource. The token is based on the managed identities for Azure resources service principal. The token is suitable for use as a bearer token in service-to-service calls requiring client credentials.

## Implement Azure App Configuration

Azure App Configuration provides a service to centrally manage application settings and feature flags.
The easiest way to add an App Configuration store to your application is through a client library that Microsoft provides.
Azure App Configuration stores configuration data as key-value pairs.
App Configuration doesn't version key values automatically as they're modified. Use labels as a way to create multiple versions of a key value. For example, you can input an application version number or a Git commit ID in labels to identify key values associated with a particular software build.

### Manage application features

Feature management is a modern software-development practice that decouples feature release from code deployment and enables quick changes to feature availability on demand. It uses a technique called feature flags (also known as feature toggles, feature switches, and so on) to dynamically administer a feature's lifecycle.

Here are several new terms related to feature management:

- Feature flag: A feature flag is a variable with a binary state of on or off. The feature flag also has an associated code block. The state of the feature flag triggers whether the code block runs or not.
- Feature manager: A feature manager is an application package that handles the lifecycle of all the feature flags in an application. The feature manager typically provides additional functionality, such as caching feature flags and updating their states.
- Filter: A filter is a rule for evaluating the state of a feature flag. A user group, a device or browser type, a geographic location, and a time window are all examples of what a filter can represent.
The feature manager supports appsettings.json as a configuration source for feature flags

#### Feature flag repository

To use feature flags effectively, you need to externalize all the feature flags used in an application. This approach allows you to change feature flag states without modifying and redeploying the application itself.

Azure App Configuration is designed to be a centralized repository for feature flags

### Secure app configuration data

Azure App Configuration encrypts sensitive information at rest using a 256-bit AES encryption key provided by Microsoft. Every App Configuration instance has its own encryption key managed by the service and used to encrypt sensitive information.

#### Enable customer-managed key capability

The following components are required to successfully enable the customer-managed key capability for Azure App Configuration:

- Standard tier Azure App Configuration instance
- Azure Key Vault with soft-delete and purge-protection features enabled
- An RSA or RSA-HSM key within the Key Vault: The key must not be expired, it must be enabled, and it must have both wrap and unwrap capabilities enabled
