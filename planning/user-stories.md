# User Stories

Vine IAM is a service that provides identity and access management functionalities for the Vine ecosystem. 
It allows users to create and manage their identities, authenticate using various methods, and control access to 
resources across different services.

## Users 
1. Developer 
2. End User
3. Administrator
4. 3rd Party Service

## Developer Stories
* As a developer, I want to integrate Vine IAM into my application so that I can manage user authentication and authorization.
* As a developer, I want to use OAuth2 and OpenID Connect protocols so that I can provide secure access to my application.
* As a developer, I want to specify custom roles and permissions so that I can control access to different parts of my application.
* As a developer, I want to use JWT tokens for authentication so that I can ensure secure communication between my application and Vine IAM.
* As a developer, I want to be able to run Vine IAM as a standalone service or as part of a microservices architecture so that I can choose the best deployment option for my needs.
* As a developer I want to be able to use PKCE for secure authorization flows so that I can enhance the security of my application.
* As a developer I want to be able to support server-to-server authentication so that my services can communicate securely.
* As a developer I want to be able to enable/disable user registration so that I can control how users sign up for my application.
* As a developer I want to be able to configure password policies so that I can enforce security standards for user accounts.
* As a developer I want to be able to configure session timeouts so that I can enhance the security of user sessions.
* As a developer I want to be able to be able to things via command line, environment variables, and configuration files so that I can choose the best way to manage settings.
* As a developer I want to be able to support SOLID-OIDC authentication so that I can integrate with decentralized identity systems.
* As a developer I want to be able to support mTLS authentication so that I can ensure secure communication between services.
* As a developer I want to be able to configure how transactional emails are sent (e.g. SMTP, SendGrid) so that I can choose the best email service for my needs.

## End User Stories
* As an end user, I want to create an account so that I can access the services provided by Vine IAM.
* As an end user, I want to log in using my email and password so that I can securely access my account.
* As an end user, I want to reset my password if I forget it so that I can regain access to my account.
* As an end user, I want to update my profile information so that I can keep my account details current.
* As an end user, I want to view my active sessions so that I can monitor my account activity.
* As an end user, I want to log out from all devices so that I can ensure my account is secure.
* As an end user I want to be add multiple users to an account so that I can share access with family or team members.
* As an end user I want to be able to login with third party services (e.g. Google, Facebook) so that I can use my existing accounts.
* As an end user I want to be able to link multiple identities to my account so that I can have flexible access options.
* As an end user I want to be able to view and manage my linked identities so that I can control how I access my account.
* As an end user I want to be able to share access to 3rd party services with an account or a user so that I can collaborate with others.

## 3rd Party Service Stories
* As a 3rd party service, I want to authenticate users using Vine IAM so that I can provide secure access to my service.
* As a 3rd party service, I want to verify JWT tokens issued by Vine IAM so that I can ensure secure communication with my service.
* As a 3rd party service, I want to request specific scopes and permissions so that I can control access to my service.
* As a 3rd party service, I want to handle token expiration and refresh so that I can maintain secure sessions for my users.
* As a 3rd party service I want to be able to integrate with Vine IAM using standard protocols (e.g. OAuth2, OpenID Connect) so that I can ensure compatibility with my existing systems.
* As a 3rd party service I want to be able to support mTLS authentication so that I can ensure secure communication between services.
* As a 3rd party service I want to be able to dynamically register my service with Vine IAM so that I can easily integrate and manage my service.