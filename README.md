![](./logo/janssen-project.jpg)

# Quick Start

Try first, ask questions later? Here's how to deploy Janssen

1. Cloud Native

```

...


```

2. VM

```

...


```

# Brief Description

The goal of the Janssen Project is to enable organizations to build a scalable centralized authentication and authorization service using free open source software. The components of the project today include implementations of the OAuth, OpenID Connect and FIDO standards. New components may be added as the technology evolves.

# Project Goal

Using the Janssen Project components, you can build a world class cloud native identity and access management ("IAM") platform.

But why should you? Why not outsource IAM to a SaaS? Well, if you can, you should! But there are a few reasons why you may not want to:
1. Your service has a large number of users or devices
2. The privacy or security profile of a hosted solution is not acceptable
3. You need the freedom to customize
4. You need to embed this component in your solution  

Ultimately, the goal of the project is to coalesce a community, and to foster an ecosystem. Many organizations, working together, can build Janssen together more effectively then any individual organization, resulting in more innovation and better code.

# Project Structure

Janssen is a Linux Foundation project, governed according to the [charter](./community/charter.md). Technical oversight of the project is the responsibility of the Technical Steering Committee ("TSC"). Day to day decision making is in the hands of the Contributors. The TSC helps to guide the direction of the project and to improve the quality and security of the development process.

# Design Goals

To a large extent, we are aligning with the goals of cloud native infrastructure. But beyond that, there are a few goals that are specific to IAM:

1. High Concurrency: In IAM, the number of users is not necessarily related to performance. If you have a billion users who never login, you can accomplish this very easily! What's hard is concurrency. If you have a billion users who all login on the same day, that's hard. Janssen is designed to horizontally scale.  That means that any concurrency is attainable if you can add more compute and memory.

2. Highly Available: IAM is mission critical--many applications may depend on it. Leveraging cloud native design is helpful. But robustness is a fundamental consideration at all phases of development.

3. Highly Flexible: Open source gives you the freedom to modify the code. But having your own fork of the code might make it hard to upgrade--you'll have to merge changes. Janssen provides standard interfaces that make it possible to implement custom business logic in an upgrade-friendly manner.  

# History

The initial code was ported by [Gluu](https://gluu.org), based on the latest version of it's IAM platform. Gluu launched in 2009 with the goal of creating an enterprise-grade open source distribution of IAM components, some of which they wrote, some of which they adopted from existing projects. In 2012, Gluu started work on an oxAuth Authorization Server to implement OpenID Connect, which they saw as a promising next-generation replacement for SAML. This project was called [oxAuth](https://github.com/GluuFederation/oxauth), and over time, became the core component of the Gluu Server.  Gluu has
submitted many [self-certifications](https://openid.net/certification) at the [OpenID Foundation](https://openid.net). Today, it is considered one of the most comprehensive OpenID Connect Providers.

In 2020, Gluu decided to democratize the governance of the oxAuth project. The name of the project was changed from oxAuth to Janssen, to avoid any potential trademark issues. There were several reasons why Gluu decided to move the project. First, Gluu management wanted to signal to the community that our core software would always be open source. Second, Gluu felt that a collaboration with the Linux Foundation would help to build a larger ecosystem. Finally, Gluu felt that inviting other organizations to collaborate would result in better code and more innovation.

# Why the name Janssen?

Pigeons (or doves if you like...) are universally regarded as a symbol of peace. But they are also fast. Powered by a handful of seeds, a well trained racing pigeon can fly 1000 kilometers in a day. The Janssen brothers of Arendonk in Belgium bred the world's fastest family of racing pigeons. Complex open source infrastructure, like breeding, requires incremental improvement. Janssen racing pigeons revolutionized the sport. The Janssen Project seeks to revolutionize identity and access management.   

# Projects

An IAM system is not a big monolith--it's a lot of services working together. Whether you deploy Janssen to a Kubernetes cluster, or you are a developer running everything on one server, it's important to understand the different parts. While there are some advantages to having one uber-project, we decided multiple projects gives us more agility to innovate individual components. Below is an overview of the different projects.

  1. **auth-server**: This component is the OAuth Authorization Server, the OpenID Connect Provider, the UMA Authorization Server--this is the main Internet facing component of Janssen. It's the service that returns tokens, JWT's and identity assertions. This service must be Internet facing.

  1. **fido**:  This component provides the server side endpoints to enroll and validate devices that use FIDO. It provides both FIDO U2F (register, authenticate) and FIDO 2 (attestation, assertion) endpoints. This service must be internet facing.

  1. **config-api**: The API to configure the auth-server and other components is consolidated in this component. This service should not be Internet-facing.

  1. **scim**: You'll need to get add, edit and update user information to you identity and access management system.  This component implements a [SCIM API](http://www.simplecloud.info/), an IETF standard JSON/REST interface for accomplishing this. This service should not be Internet facing.

  1. **admin-ui**: While you can call API's to configure everything in Janssen, that's no fun for _ad hoc_ admin tasks. This service calls the config-api for you, logging the corresponding calls for your reference. This service should not be Internet facing.  

  1. **eleven**: This is a PKCS11 REST API that can be used for key operations by the auth-server in lieu of local storage of private keys. This should service should not be Internet facing.

  1. **auth-client**: Middleware API to help application developers call an OAuth, OpenID or UMA server. You may wonder why this is necessary? But client developers have trouble using advanced OpenID signing and encryption features. This API provides some high level API's to do some of the heavy lifting.

  1. **setup**: Deploying all these components into one working system is tricky. The setup script provides an easy way to do this in one VM. You can also use the cloud native way. But some developers may find this burdensome.

  1. **core**: This library has code that is shared across several janssen projects. You will most likely need this project when you build other Janssen components.

  1. **orm**: This is the library for persistence and caching implemenations in Janssen. Currently LDAP and Couchbase are supported. RDBMS is coming soon.

  1. **docker-**: These are the projects that contain the container code.

# Support

While we're getting started, you may want to refer to the [Gluu 4.2 docs](https://gluu.org/docs/gluu-server/4.2/). While not everything is going to be the same, there is a lot of good info there.

We have setup a [community chat on Gitter](https://gitter.im/JanssenProject/Lobby). You can register for free their with your Github identity.

If you find a bug in a Janssen project, or you would like to suggest a new feature, try the chat first. Then raise an issue on the respective repository. If you have a "howto" or "usage" question, raise the issue on the [Janssen Home](https://github.com/JanssenProject/home) project.
