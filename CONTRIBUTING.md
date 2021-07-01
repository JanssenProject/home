---
layout: default
---

# Join the community

* Chat: We have an active [community chat on Gitter](https://gitter.im/JanssenProject/Lobby). You can register for free their with your Github identity.
* Tweet: Janssen is on [Twitter](https://twitter.com/janssen_project) too. Follow us there to stay up to date on release announcements and news around Janssen.
* Email: We have an active mailing list also.
* Google group : You can subscribe to the [Janssen Google Group](https://groups.google.com/u/2/g/janssen_project)
  and post messages there.

# Contribute

Please go through our [contribution guidelines](#contribution-guidelines) so it'll be easy for you to make successful contributions.

In case you are _**first-time**_ contributor, then you can start with our [good first issues list](https://github.com/JanssenProject/home/labels/good%20first%20issue) These are issues where you can easily contribute and community members will guide and support your contribution as always.

If you need Janssen installation to test out your fix, here are the [steps](#janssen-quick-install).

There are many ways of contributing to Janssen. And it is not just limited to fixing issues and raising pull requests. Janssen welcomes you to [raise issues](#issues), respond to queries, suggest new features, tell us your experience with Janssen, be it good or bad. All these are contributions towards making Janssen better.

# Janssen quick install

Try first, ask questions later? Here's how to deploy Janssen

## Option 1: Cloud Native with microk8s

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


# Contribution guidelines

We are really glad you are reading this, because we need volunteer developers to help this project come to fruition.

- [Code of Conduct](#code-of-conduct)
- [Community](#community)
- [Issues](#issues)
  - [Triaging](#triaging)
    - [More about labels](#more-about-labels)
- [Pull Requests](#pull-requests)
  - [Commit convention](#commit-convention)
    - [Rule type](#rule-type)
- [Coding Guidelines](#coding-guidelines)
  - [C++](#c)
- [Developer Certificate Of Origin](#developer-certificate-of-origin)

## Code of Conduct

Janssen project has a
[Code of Conduct](./community/code-of-conduct.md)
to which all contributors must adhere, please read it before interacting with the repository or the community in any way.

## Community

We have setup a [community chat on Gitter](https://gitter.im/JanssenProject/Lobby). Please raise discussion and **support requests** here first.

Join the [google group mailing list](https://groups.google.com/u/2/g/janssen_project) for news, announcements, and a Google calendar invite for our community open source meetings.

We also run monthly **community calls** which are open calls to discuss Janssen projects from an user perspective. In case you want to discuss a topic during those calls you can simply propose it opening an issue in the [community](https://github.com/JanssenProject/community) repository and join the call!

## Issues

There are four kinds of issues you can open:

- Bug report: you believe you found a problem in a project and you want to discuss and get it fixed,
  creating an issue with the **bug report template** is the best way to do so.
- Enhancement: any kind of new feature need to be discussed in this kind of issue, do you want a new rule or a new feature? This is the kind of issue you want to open. Be very good at explaining your intent, it's always important that others can understand what you mean in order to discuss, be open and collaborative in letting others help you getting this done!
- Vulnerability: If you identify a security problem, please report it immediately, providing details about the nature, and if applicable, how to reproduce it. If you want to report an issue privately, you can email security@gluu.org
- Failing tests: you noticed a flaky test or a problem with a build? This is the kind of issue to triage that!

The best way to get **involved** in the project is through issues, you can help in many ways:

- Issues triaging: participating in the discussion and adding details to open issues is always a good thing,
  sometimes issues need to be verified, you could be the one writing a test case to fix a bug!
- Helping to resolve the issue: you can help in getting it fixed in many ways, more often by opening a pull request. In case you are _**first-time**_ contributor, then you can start with our [good first issues list](https://github.com/JanssenProject/home/labels/good%20first%20issue) These are issues where you can easily contribute and community members will guide and support your contribution as always.

### Triaging

We need help in categorizing issues. Thus any help is welcome!

When you triage an issue, you:

* assess whether it has merit or not

* quickly close it by correctly answering a question

* point the reporter to a resource or documentation answering the issue

* tag it via labels, projects, or milestones

* take ownership submitting a PR for it, in case you want 😇

#### More about labels

These guidelines are not set in stone and are subject to change.

Anyway a `kind/*` label for any issue is mandatory.

This is the current [label set](https://github.com/falcosecurity/falco/labels) we have.

You can use commands - eg., `/label <some-label>` to add (or remove) labels or manually do it.

The commands available are the following ones:

```
/[remove-](area|kind|priority|triage|label)
```

Some examples:

* `/area rules`
* `/remove-area rules`
* `/kind kernel-module`
* `/label good-first-issue`
* `/triage duplicate`
* `/triage unresolved`
* `/triage not-reproducible`
* `/triage support`
* ...

## Pull Requests

Thanks for taking time to make a [pull request](https://help.github.com/articles/about-pull-requests) (hereafter PR).

In the PR body, feel free to add an area label if appropriate by typing `/area <AREA>`, PRs will also
need a kind, make sure to specify the appropriate one by typing `/kind <KIND>`.

The PR template is there to guide you through the process of opening it.

Also feel free to suggest a reviewer with `/cc @theirname`, or to assign an assignee using `/assign @nickname`.

Once your reviewer is happy, they will say `/lgtm` which will apply the
`lgtm` label, and will apply the `approved` label if they are an
[owner](/OWNERS) for the project.

Your PR will be automatically merged once it has the `lgtm` and `approved`
labels, does not have any `do-not-merge/*` labels, and all status checks (eg., rebase, tests, DCO) are positive.

### Commit convention

As commit convention, we adopt [Conventional Commits v1.0.0](https://www.conventionalcommits.org/en/v1.0.0/), we have an history
of commits that do not adopt the convention but any new commit must follow it to be eligible for merge.

#### Rule type

Besides the classic types, we adopt a type for rules, `rule(<scope>):`.
Example:

```
rule(Write below monitored dir): make sure monitored dirs are monitored.
```

Each rule change must be on its own commit, if a change to a macro is done while changing a rule they can go together but only one rule per commit must happen.

If you are changing only a macro, the commit will look like this:

```
rule(macro user_known_write_monitored_dir_conditions): make sure conditions are great
```

## Coding Guidelines


## Developer Certificate Of Origin

The [Developer Certificate of Origin (DCO)](https://developercertificate.org/) is a lightweight way for contributors to certify that they wrote or otherwise have the right to submit the code they are contributing to the project.

Contributors to the Janssen project sign-off that they adhere to these requirements by adding a `Signed-off-by` line to commit messages.

```
This is a commit message

Signed-off-by: Foo Bar <foobar@spam.org>
```

Git even has a `-s` command line option to append this automatically to your commit message:

```
$ git commit -s -m 'This is my commit message'
```


# Reference links

