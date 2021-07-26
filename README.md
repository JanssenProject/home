[![Codacy Badge](https://app.codacy.com/project/badge/Grade/c9f92d07195b48cea3ed2fe7410f5b3d)](https://www.codacy.com/gh/JanssenProject/home/dashboard?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=JanssenProject/home&amp;utm_campaign=Badge_Grade)
[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/4353/badge)](https://bestpractices.coreinfrastructure.org/projects/4353)

![](./logo/janssen-project.jpg)

# Welcome to Janssen project.

Janssen enables organizations to build a scalable centralized authentication and authorization service using free open source software. The components of the project today include implementations of the OAuth, OpenID Connect and FIDO standards. New components may be added as the technology evolves.

# Get started with quick install

Start a fresh ubuntu `18.04` or `20.04` and execute the following

 ```
 sudo su -
 wget https://raw.githubusercontent.com/JanssenProject/jans-cloud-native/master/automation/startdemo.sh && chmod u+x startdemo.sh && ./startdemo.sh
 ```

This will install docker, microk8s, helm and Janssen with the default settings can be found inside [values.yaml](https://github.com/JanssenProject/jans-cloud-native/blob/master/charts/jans/values.yaml). Please map the `ip` of the instance running ubuntu to `demoexample.jans.io` and then access the endpoints at your browser such in the example in the table below. Also see [instructions for Amazon EKS](https://janssenproject.github.io/jans-cloud-native/charts/jans/)

|Service           | Example endpoint                                                       |   
|------------------|------------------------------------------------------------------------|
|Auth server       | `https://demoexample.jans.io/.well-known/openid-configuration`         |
|fido2             | `https://demoexample.jans.io/.well-known/fido2-configuration`          |
|scim              | `https://demoexample.jans.io/.well-known/scim-configuration`           |   

What you just installed is Cloud Native Janssen with microk8s. Explore [different ways](https://janssenproject.github.io/docs/source/installation-guide/) to install and deploy Janssen to suite you needs in production. 

# Get to know Janssen

Learn more and in-depth about Janssen features and module using [documentation](https://janssenproject.github.io/docs/)

# Contribute

Want to contribute? Our [Contribution](CONTRIBUTING.md) guide will get you started 
