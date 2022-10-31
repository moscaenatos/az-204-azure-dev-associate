# Implement user authentication and authorization - Table of contents

- [Implement user authentication and authorization - Table of contents](#implement-user-authentication-and-authorization---table-of-contents)
  - [Explore the Microsoft identity platform](#explore-the-microsoft-identity-platform)
    - [Explore service principals](#explore-service-principals)
      - [Application object](#application-object)
      - [Service principal object](#service-principal-object)
      - [Relationship between application objects and service principals](#relationship-between-application-objects-and-service-principals)
    - [Discover permissions and consent](#discover-permissions-and-consent)
      - [Permission types](#permission-types)
      - [Discover conditional access](#discover-conditional-access)
    - [Implement authentication by using the Microsoft Authentication Library](#implement-authentication-by-using-the-microsoft-authentication-library)
      - [Public client, and confidential client applications](#public-client-and-confidential-client-applications)
      - [Initialize client applications](#initialize-client-applications)
    - [Implement shared access signatures](#implement-shared-access-signatures)
      - [Types of shared access signatures](#types-of-shared-access-signatures)
      - [Choose when to use shared access signatures](#choose-when-to-use-shared-access-signatures)
      - [Explore stored access policies](#explore-stored-access-policies)
    - [Explore Microsoft Graph](#explore-microsoft-graph)
      - [Authentication](#authentication)

## Explore the Microsoft identity platform

The Microsoft identity platform for developers is a set of tools which includes authentication service, open-source libraries, and application management tools.

There are several components that make up the Microsoft identity platform:

- OAuth 2.0 and OpenID Connect standard-compliant authentication service enabling developers to authenticate several identity types, including:
  - Work or school accounts, provisioned through Azure Active Directory
  - Personal Microsoft account, like Skype, Xbox, and Outlook.com
  - Social or local accounts, by using Azure Active Directory B2C
- Open-source libraries: Microsoft Authentication Libraries (MSAL) and support for other standards-compliant libraries
- Application management portal: A registration and configuration experience in the Azure portal, along with the other Azure management capabilities.
- Application configuration API and PowerShell: Programmatic configuration of your applications through the Microsoft Graph API and PowerShell so you can automate your DevOps tasks.

### Explore service principals

To delegate Identity and Access Management functions to Azure Active Directory, an application must be registered with an Azure Active Directory tenant. When you register your application with Azure Active Directory, you're creating an identity configuration for your application that allows it to integrate with Azure Active Directory. When you register an app in the Azure portal, you choose whether it is:

- Single tenant: only accessible in your tenant
- Multi-tenant: accessible in other tenants

#### Application object

An Azure Active Directory application is defined by its one and only application object, which resides in the Azure Active Directory tenant where the application was registered
An application object is used as a template or blueprint to create one or more service principal objects. A service principal is created in every tenant where the application is used.
The application object describes three aspects of an application:

- how the service can issue tokens in order to access the application
- resources that the application might need to access
- and the actions that the application can take.

#### Service principal object

To access resources that are secured by an Azure Active Directory tenant, the entity that requires access must be represented by a security principal. This is true for both users (user principal) and applications (service principal).
The security principal defines the access policy and permissions for the user/application in the Azure Active Directory tenant. This enables core features such as authentication of the user/application during sign-in, and authorization during resource access.

There are three types of service principal:

- Application - This type of service principal is the local representation, or application instance, of a global application object in a single tenant or directory. A service principal is created in each tenant where the application is used and references the globally unique app object. The service principal object defines what the app can actually do in the specific tenant, who can access the app, and what resources the app can access.

- Managed identity - This type of service principal is used to represent a managed identity. Managed identities provide an identity for applications to use when connecting to resources that support Azure Active Directory authentication. When a managed identity is enabled, a service principal representing that managed identity is created in your tenant. Service principals representing managed identities can be granted access and permissions, but cannot be updated or modified directly.

- Legacy - This type of service principal represents a legacy app, which is an app created before app registrations were introduced or an app created through legacy experiences. A legacy service principal can have credentials, service principal names, reply URLs, and other properties that an authorized user can edit, but does not have an associated app registration. The service principal can only be used in the tenant where it was created.

#### Relationship between application objects and service principals

The application object is the global representation of your application for use across all tenants, and the service principal is the local representation for use in a specific tenant. The application object serves as the template from which common and default properties are derived for use in creating corresponding service principal objects.

An application object has:

- A 1:1 relationship with the software application, and
- A 1:many relationship with its corresponding service principal object(s).
A service principal must be created in each tenant where the application is used, enabling it to establish an identity for sign-in and/or access to resources being secured by the tenant. A single-tenant application has only one service principal (in its home tenant), created and consented for use during application registration. A multi-tenant application also has a service principal created in each tenant where a user from that tenant has consented to its use.

### Discover permissions and consent

Applications that integrate with the Microsoft identity platform follow an authorization model that gives users and administrators control over how data can be accessed.

The Microsoft identity platform implements the OAuth 2.0 authorization protocol. OAuth 2.0 is a method through which a third-party app can access web-hosted resources on behalf of a user.
In OAuth 2.0, these types of permission sets are called scopes. They're also often referred to as permissions. In the Microsoft identity platform, a permission is represented as a string value. An app requests the permissions it needs by specifying the permission in the scope query parameter.

#### Permission types

The Microsoft identity platform supports two types of permissions: delegated permissions and application permissions.

- Delegated permissions are used by apps that have a signed-in user present. For these apps, either the user or an administrator consents to the permissions that the app requests. The app is delegated with the permission to act as a signed-in user when it makes calls to the target resource.

- Application permissions are used by apps that run without a signed-in user present, for example, apps that run as background services or daemons. Only an administrator can consent to application permissions.

 If you are defining permissions, you will also need to understand how your users will gain access to your app or API.

There are three consent types:

- static user consent: you must specify all the permissions it needs in the app's configuration in the Azure portal. Some possible issues for developers:
  - The app needs to request all the permissions it would ever need upon the user's first sign-in. This can lead to a long list of permissions that discourages end users from approving the app's access on initial sign-in.
  - The app needs to know all of the resources it would ever access ahead of time. It is difficult to create apps that could access an arbitrary number of resources.

- incremental and dynamic user consent:  You can ask for a minimum set of permissions upfront and request more over time as the customer uses additional app features.If the user       hasn't yet consented to new scopes added to the request, they'll be prompted to consent only to the new permissions. Incremental, or dynamic consent, only applies to delegated     permissions and not to application permissions.
- admin consent: required when your app needs access to certain high-privilege permissions.
  - ensures that administrators have some additional controls before authorizing apps or users to access highly privileged data from the organization.
  - Admin consent done on behalf of an organization still requires the static permissions registered for the app. Set those permissions

#### Discover conditional access

The Conditional Access feature in Azure Active Directory offers one of several ways that you can use to secure your app and protect a service. Conditional Access enables developers and enterprise customers to protect services in a multitude of ways including:

- Multifactor authentication
- Allowing only Intune enrolled devices to access specific services
- Restricting user locations and IP ranges

### Implement authentication by using the Microsoft Authentication Library

The Microsoft Authentication Library (MSAL) enables developers to acquire tokens from the Microsoft identity platform in order to authenticate users and access secured web APIs.
The Microsoft Authentication Library (MSAL) can be used to provide secure access to Microsoft Graph, other Microsoft APIs, third-party web APIs, or your own web API. MSAL supports many different application architectures and platforms including .NET, JavaScript, Java, Python, Android, and iOS.

#### Public client, and confidential client applications

Security tokens can be acquired by multiple types of applications. These applications tend to be separated into the following two categories. Each is used with different libraries and objects.

- Public client applications: Are apps that run on devices or desktop computers or in a web browser. They're not trusted to safely keep application secrets, so they only access web APIs on behalf of the user. (They support only public client flows.) Public clients can't hold configuration-time secrets, so they don't have client secrets.
- Confidential client applications: Are apps that run on servers (web apps, web API apps, or even service/daemon apps). They're considered difficult to access, and for that reason capable of keeping an application secret. Confidential clients can hold configuration-time secrets. Each instance of the client has a distinct configuration (including client ID and client secret).

#### Initialize client applications

With MSAL.NET 3.x, the recommended way to instantiate an application is by using the application builders: PublicClientApplicationBuilder and ConfidentialClientApplicationBuilder. They offer a powerful mechanism to configure the application either from the code, or from a configuration file, or even by mixing both approaches.
Before initializing an application, you first need to register it so that your app can be integrated with the Microsoft identity platform. After registration, you may need the following information (which can be found in the Azure portal):

- The client ID (a string representing a GUID)
- The identity provider URL (named the instance) and the sign-in audience for your application. These two parameters are collectively known as the authority.
- The tenant ID if you are writing a line of business application solely for your organization (also named single-tenant application).
- The application secret (client secret string) or certificate (of type X509Certificate2) if it's a confidential client app.
- For web apps, and sometimes for public client apps (in particular when your app needs to use a broker), you'll have also set the redirectUri where the identity provider will contact back your application with the security tokens.

### Implement shared access signatures

A shared access signature (SAS) is a URI that grants restricted access rights to Azure Storage resources. You can provide a shared access signature to clients that you want to grant delegate access to certain storage account resources.

#### Types of shared access signatures

Azure Storage supports three types of shared access signatures:

- User delegation SAS: A user delegation SAS is secured with Azure Active Directory credentials and also by the permissions specified for the SAS.
  - Blob storage only.
- Service SAS: A service SAS is secured with the storage account key. A service SAS delegates access to a resource in the following Azure Storage services:
  - Blob storage
  - Queue storage
  - Table storage
  - Azure Files
- Account SAS: An account SAS is secured with the storage account key. An account SAS delegates access to resources in one or more of the storage services.
  - All of the operations available via a service or user delegation SAS are also available via an account SAS.

The most secure SAS is a user delegation SAS. Use it wherever possible because it removes the need to store your storage account key in code. You must use Azure Active Directory to manage credentials. This option might not be possible for your solution.

#### Choose when to use shared access signatures

Use a SAS when you want to provide secure access to resources in your storage account to any client who does not otherwise have permissions to those resources.

a SAS is required to authorize access to the source object in a copy operation in certain scenarios:

When you copy a blob to another blob that resides in a different storage account, you must use a SAS to authorize access to the source blob. You can optionally use a SAS to authorize access to the destination blob as well.

When you copy a file to another file that resides in a different storage account, you must use a SAS to authorize access to the source file. You can optionally use a SAS to authorize access to the destination file as well.

When you copy a blob to a file, or a file to a blob, you must use a SAS to authorize access to the source object, even if the source and destination objects reside within the same storage account.

#### Explore stored access policies

A stored access policy provides an additional level of control over service-level shared access signatures (SAS) on the server side. Establishing a stored access policy groups shared access signatures and provides additional restrictions for signatures that are bound by the policy.
The following storage resources support stored access policies:

- Blob containers
- File shares
- Queues
- Tables

To revoke a stored access policy you can delete it, rename it by changing the signed identifier, or change the expiry time to a value in the past.
To remove a single access policy, call the resource's Set ACL operation, passing in the set of signed identifiers that you wish to maintain on the container. To remove all access policies from the resource, call the Set ACL operation with an empty request body.

The most flexible and secure way to use a service or account SAS is to associate the SAS tokens with a stored access policy.

### Explore Microsoft Graph

Microsoft Graph is the gateway to data and intelligence in Microsoft 365. It provides a unified programmability model that you can use to access the tremendous amount of data in Microsoft 365, Windows 10, and Enterprise Mobility + Security.

In the Microsoft 365 platform, three main components facilitate the access and flow of data:

- The Microsoft Graph API offers a single endpoint, <https://graph.microsoft.com>. You can use REST APIs or SDKs to access the endpoint. Microsoft Graph also includes a powerful set of services that manage user and device identity, access, compliance, security, and help protect organizations from data leakage or loss.

- Microsoft Graph connectors work in the incoming direction, delivering data external to the Microsoft cloud into Microsoft Graph services and applications, to enhance Microsoft 365 experiences such as Microsoft Search. Connectors exist for many commonly used data sources such as Box, Google Drive, Jira, and Salesforce.

- Microsoft Graph Data Connect provides a set of tools to streamline secure and scalable delivery of Microsoft Graph data to popular Azure data stores. The cached data serves as data sources for Azure development tools that you can use to build intelligent applications.

#### Authentication

To access the data in Microsoft Graph, your application will need to acquire an OAuth 2.0 access token, and present it to Microsoft Graph in either of the following:

- The HTTP Authorization request header, as a Bearer token
- The graph client constructor, when using a Microsoft Graph client library
Use the Microsoft Authentication Library API, MSAL to acquire the access token to Microsoft Graph.
