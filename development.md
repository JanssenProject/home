# Setting up workspace Janssen development
## Overview

This is a guide for setting up a development environment that enables compilation and testing of Janssen components. For a complete development environment, you need three things: 

1) Janssen server for development
2) Workspace which has code and tests that you can run 
3) Ability to remote debug 

In this guide, we will see how to setup all three above. You can setup both 1) and 2) on same machine or on different machines.

## Installing Janssen server:

When you plan to develop and contribute code to Janssen, you need a running Janssen server in order to run your tests and verify that your feature or bug fix is working as intended. You can plan to install Janssen server on local Windows machine, Linux machine or you may planning to install Janssen using Janssen cloud based deployment. 

### Hardware requirements of Janssen server

- 8 GB RAM for Janssen server installation
- CPU with at least two cores
- 40 GB harddisk space

### Platform requirements of Janssen server

Janssen auth server installs on Linux based operating system, primarily, Ubuntu. If your development environment is using a different operating system, like Windows, we have to leverage virtualization tools to install Janssen server. Follow the instructions listed in sections below as appropriate.

1) [Install Janssen on Windows OS](https://github.com/ossdhaval/home/blob/main/development.md#installing-janssen-auth-server-on-windows-machine)
2) [Install Janssen on Ubuntu 20.4](https://github.com/ossdhaval/home/blob/main/development.md#install-janssen)
3) [Install Janssen on remote server](https://github.com/ossdhaval/home/blob/main/development.md#installing-janssen-auth-server-on-remote-server) ( like a Google Cloud Platform or AWS Linux instance )
4) On Cloud based deployment

### Installing Janssen auth server on Windows machine

Janssen runs on Linux based operating system Ubuntu. In order to run it on a Windows machine, we have to leverage virtualization technique as described in steps below.

#### Prepare your Windows machine

Steps:

    1) `TODO`
    2) `TODO`

Once you have executed above steps, your Windows machine is ready for [Janssen installation](https://github.com/ossdhaval/home/blob/main/development.md#install-janssen).

### Installing Janssen auth server on Ubuntu

#### Prepare your Ubuntu system

Steps:

    1) `TODO`
    2) `TODO`

Once you have executed above steps, your Ubuntu machine is ready for [Janssen installation](https://github.com/ossdhaval/home/blob/main/development.md#install-janssen).

### Installing Janssen auth server on remote server

If you have limited hardware resources on your local machine, then you may install Janssen on a remote server. For this you need to have access to a remote server, either standalone or an instance hosted on any cloud platform like GCP or AWS. Here in this guide, we will take example of installing Janssen server on an GCP instance.

#### Prepare GCP instance

Steps:

    1) `TODO`
    2) `TODO`

Once you have executed above steps, your GCP instance is ready for [Janssen installation](https://github.com/ossdhaval/home/blob/main/development.md#install-janssen).

## Install Janssen

Janssen installation involves running two commands. During installation, setup will prompt few questions and you'll also need to accept license. During this process, among other inputs, the setup will also ask you to name your Janssen server. You can name it anyting but make note of it, as we will need that name in future. For now, we will assume that server is named *demoexample.jans.io*.

First command to run is as below. It'll download required installation script.

`wget https://raw.githubusercontent.com/JanssenProject/jans-setup/master/install.py`

Once you have the script, you can start installation. You can either start installation with or without test data loading along with it. Test data load will help you to run unit tests when you are writing code. You can also choose to load it later when needed.

To install with test data load:

`sudo python3 install.py –args="-t"`

To install without test data load:

`sudo python3 install.py`

Load test data separately from installation process: 

```
cd /opt/jans/jans-setup/
sudo python3 setup.py -t -x -n
```

After successful execution, you have a working Janssen setup. 

Now, to access Janssen end-points, please map `IP` address of your Janssen server with name of Janssen server which you provided during install process. This mapping needs to be in `/etc/hosts` file of host from where you intend to access the end-point URLs. Once this mapping is done, you can access below mentioned end-points to verify your Janssen installation:


    |Service           | Example endpoint                                                       |   
    |------------------|------------------------------------------------------------------------|
    |Auth server       | `https://demoexample.jans.io/.well-known/openid-configuration`         |
    |fido2             | `https://demoexample.jans.io/.well-known/fido2-configuration`          |
    |scim              | `https://demoexample.jans.io/.well-known/scim-configuration`           |   
    

## Setup your workspace

#### Minimal software requirements

Most basic tools required are
- `git`  : To clone and manage the inav code repository
- `java` : Java to run maven and build. Needs Java 1.8 or above
- `mvn`  : latest maven
- Other tools like IDE etc can be of your choice

Setting up your workspace is as easy as cloning Janssen module repository from Github. You can clone the repository of the module that you intend to work on.

Here, we are going ahead with Janssen auth server module.

#### Get code

You can get code for Janssen modules from Github repository [here](https://github.com/JanssenProject)

In the directory where you want to create your workspace, you can clone the module as below:

`git clone https://github.com/JanssenProject/jans-auth-server`

#### Build

`cd jans-auth-server`

At this point, you should be able to successfully compile the module using `mvn compile`

#### Run tests

In order to be able to run tests, your code in your local workspace should be able to connect to Janssen server that you installed previously. This is done by creating a `profile` and a few more commands. That is what we will do next.

##### Create test profile 

###### What is a profile:

At the most basic level, a profile is a directory, with the same name as your server and contains files that enable your code to connect with Janssen server. In following steps, We will create profile directory where your code is and put required files under it. During it's installation, the Janssen server has already created these files for us to copy.

1) Get profile name which we will be using. Run this command where Janssen server is installated.

`cat /opt/jans/jans-setup/output/test/jans-auth/client/config-oxauth-test-data.properties | grep clientKeyStoreFile | cut -d "/" -f2 | cut -d "/" -f1`

 Output of this command is the profile name that you need to use in following steps. Generally, this name is same as name of your Janssen server.

2) Create and setup your profile
 - Setup profile for *client* module of *jans-auth-server*
   - `mkdir ./client/profiles/<profile_name>/`
   - `cp -rf /opt/jans/jans-setup/output/test/jans-auth/client/* ./client/profiles/<profile_name>/`
   - `cp ./client/profiles/default/client_keystore.jks ./client/profiles/<profile_name>/`
 - Setup profile for *server* module of *jans-auth-server*
   - `mkdir ./server/profiles/<profile_name>/`
   - `cp -rf /opt/jans/jans-setup/output/test/jans-auth/server/* ./server/profiles/<profile_name>/`
   - `cp ./server/profiles/default/client_keystore.jks ./server/profiles/<profile_name>/`

If your code and installed Janssen server is on the same machine, then at this point you should be able to [run full build](https://github.com/ossdhaval/home/blob/main/development.md#run-full-build-with-tests) with tests. If both are on separate machines then you need to take [few additional steps](https://github.com/ossdhaval/home/blob/main/development.md#additional-steps-for-remote-workspace) before you can run your full build with tests.

##### Additional steps for remote workspace

These steps are only needed if we need to run build and tests on a different machine then the one where you have installed Janssen server. Also its not needed if we deploy CA cert into Janssen instalation.

1) Import self signed http cert into java truststore.

Copy `/etc/certs/httpd.crt` from CE server to `<path>/httpd.crt` or run:

`openssl s_client -connect <ce-server>:443 2>&1 |sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/httpd.crt`

`/opt/jre/bin/keytool -import -alias jans_http -keystore /opt/jre/lib/security/cacerts -file /tmp/httpd.crt`


2) Fill the right configuration `cibaEndUserNotificationConfig`. It's in `jansConfDyn` in ou=jans-auth,ou=configuration,o=jans. After restart Jans Auth server:

```
systemctl restart jans-auth
```

##### Run full build with tests:

```
mvn -fae -Dcfg=<profile_name> -Dcvss-score=9 -Dfindbugs.skip=true -Ddependency.check=false clean compile package
```

You should be able to run test cases successfully.

## Setup remote debugging

#### Changes required on Janssen server

- Open service config file

  `vim /etc/default/jans-auth`

- add line below at the end of `JAVA_OPTIONS`

  `-Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=6001`

- then restart services

  `systemctl restart jans-auth.service`
  
- (only if you are using GCP instance as jans server) add port to firewall:
  - search for firewall on dashboard search
  - edit the firewall called `default-allow-ssh`, and add port `6001`. Save settings.
  - not sure but you may want to restart your instance once for firewall settings to take effect.

#### changes required in the machine where your workspace is:

- setup port forwarding
  
  `ssh -L 6001:localhost:6001 <user>@demoexample.jans.io`
  
  this command will open an ssl connection with jans server. Keep the window open.
  
- In IntellijIdea, 
  - `shift+shift` -> search for `edit configuration` -> click on `+` -> `remote jvm debugging` -> then give below values
  - `host:` remote host IP or name as in `hosts` file, `port:` 6001, `use module:` give the module which is being debugged on server. 

