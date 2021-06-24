# Setting up Janssen development environment
## Overview

This is a generic guide for setting up environment that enables compilation and testing of Janssen components. In this guide, we will setup Janssen server for development, and we will see how to build and run test for one of the Janssen component which is Janssen auth server.

Janssen supports diferent version of Linux distros. It's possible to install it into VM, LXC container, etc. For this guide, we will use Ubuntu 20.4.

## Prerequisites

During setup installer can prompt to install missing package. In addition to this it is necessary to install some other tools:

* `git`  : To clone and manage the inav code repository
* `java` : Java to run maven and build. Needs Java 1.8 or above
* `mvn`  : latest maven




## Installing Janssen server:

Janssen installation involves running two commands. During installation setup will prompt some questions and you'll also need to accept license. During the process, among other inputs, the setup will ask you to name your Janssen server. You can name it anyting but make note of it as we will need that name in future. For now, we will assume that server is named *demoexample.jans.io*.

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

Setting up your workspace is as easy as cloning Janssen module repository from Github. You can clone the repository of the module that you intend to work on.

Here, we are going ahead with Janssen auth server module.

### Clone Jans Auth

`git clone https://github.com/JanssenProject/jans-auth-server`

`cd jans-auth-server`

At this point, you should be able to successfully compile the module using `mvn compile`

In order to be able to run tests, your code in your local workspace should be able to connect to Janssen server that you installed previously. This is done by creating a profile and few more commands. That is what we will do next.

### 3. Create test profile 

#### What is a profile:
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



`NOTE:` Step 4 below is only needed if we need to run build and tests on a different machine then the one where you have installed Janssen server. Also its not needed if we deploy CA cert into Janssen instalation.

### 4) Setup remote workspace

#### 4.1 Import self signed http cert into java truststore.

Copy `/etc/certs/httpd.crt` from CE server to `<path>/httpd.crt` or run:

`openssl s_client -connect <ce-server>:443 2>&1 |sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/httpd.crt`

`/opt/jre/bin/keytool -import -alias jans_http -keystore /opt/jre/lib/security/cacerts -file /tmp/httpd.crt`


#### 4.2 Fill the right configuration `cibaEndUserNotificationConfig`. It's in `jansConfDyn` in ou=jans-auth,ou=configuration,o=jans. After restart Jans Auth server:

```
systemctl restart jans-auth
```

### 5. Run build:

```
mvn -Dcfg=<profile_name> -Dcvss-score=9 -Dfindbugs.skip=true -Ddependency.check=false clean compile package
```

You should be able to run test cases successfully.

### 6. Useful commands

- `systemctl status jans-auth.service`: To know status of Janssen auth server service
- `systemctl restart jans-auth`: Restart Janssen auth service
- `systemctl list-units --all jans*`: To know status of all Janssen services
- 
