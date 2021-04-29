---
layout: default
---

# Join the community

* Chat: We have setup a [community chat on Gitter](https://gitter.im/JanssenProject/Lobby). You can register for free their with your Github identity.
* Tweet: Janssen is on [Twitter](https://twitter.com/janssen_project) too. Follow us there to stay up to date on release announcements and news around Janssen.
* Email: We have an active mailing list also.
* Google group : You can subscribe to the [Janssen Google Group](https://groups.google.com/u/2/g/janssen_project)
  and post messages there.

# Contribute

Please go through our [contribution guidelines](https://github.com/JanssenProject/home/blob/8abc4e177868a60b913f0895a7258d931518dc4d/CONTRIBUTING.md#how-to-contribute) so it'll be easy for you to make successful contributions.

In case you are _**first-time**_ contributor, then you can start with our [good first issues list](https://github.com/JanssenProject/home/labels/good%20first%20issue) These are issues where you can easily contribute and community members will guide and support your contribution as always.

If you need Janssen installation to test out your fix, here are the [steps](https://github.com/ossdhaval/gitcheck/wiki/How-to-Contribute#janssen-quick-install).

There are many ways of contributing to Janssen. And it is not just limited to fixing issues and raising pull requests. Janssen welcomes you to [raise issues](https://github.com/JanssenProject/home/blob/8abc4e177868a60b913f0895a7258d931518dc4d/CONTRIBUTING.md#issues), respond to queries, suggest new features, tell us your experience with Janssen, be it good or bad. All these are contributions towards making Janssen better.

# Janssen quick install

Try first, ask questions later? Here's how to deploy Janssen

## Option 1: Cloud Native with microk8s

Start a fresh ubuntu `18.04` or `20.04` and execute the following

 ```
 sudo su -
 wget https://raw.githubusercontent.com/JanssenProject/jans-cloud-native/master/automation/startdemo.sh && chmod u+x startdemo.sh && ./startdemo.sh
 ```

This will install docker, microk8s, helm and Janssen with the default settings the can be found inside [values.yaml](https://github.com/JanssenProject/jans-cloud-native/blob/master/helm/values.yaml). Please map the `ip` of the instance running ubuntu to `demoexample.jans.io` and then access the endpoints at your browser such in the example in the table below. Also see [instructions for Amazon EKS](https://github.com/JanssenProject/jans-cloud-native#amazon-web-services-aws---eks)

|Service           | Example endpoint                                                       |   
|------------------|------------------------------------------------------------------------|
|Auth server       | `https://demoexample.jans.io/.well-known/openid-configuration`         |
|fido2             | `https://demoexample.jans.io/.well-known/fido2-configuration`          |
|scim              | `https://demoexample.jans.io/.well-known/scim-configuration`           |   


## Option 2: VM installation

To install:

```
# curl https://raw.githubusercontent.com/JanssenProject/jans-setup/master/install.py > install.py
# python3 install.py
```

To uninstall, run the script again with the `-uninstall` argument:

```
# python3 install.py -uninstall
```

## Option 3: You can [build jans-auth server](./development.md).

# Reference links

