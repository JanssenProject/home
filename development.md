# Generic development flow
## Overview

This article endeavours to provide a generic guide for compiling Jans Auth server.

Jans Auth server setup support diferent version of Linux distros. It's possible to install it into VM, LXC container, etc.

## Prerequisites

During setup installer can prompt to install missing package. In addition to this it is necessary to install some other tools:

* `git`  : clone and manage the inav code repository
* `java` : java to run maven and build. It supports java >= 8
* `mvn`  : latest maven




## Install Janssen into VM:

These 2 commands needed to run setup. During installation setup will prompt some questions and it's needed to accept license.
```
 wget https://raw.githubusercontent.com/JanssenProject/jans-setup/master/install.py
 python3 install.py
```

## Developement configuration:

### 1. Load test data and configuration
```
cd /opt/jans/jans-setup/
python3 setup.py -t -n
```

### 2. Clone Jans Auth
```
git clone https://github.com/JanssenProject/jans-auth-server
cd jans-auth-server
```

### 3. Create test profile `<janssen_server>` and copy test profile files into it.

```
# Get profile name
cat /opt/jans/jans-setup/output/test/jans-auth/client/config-oxauth-test-data.properties | grep clientKeyStoreFile | cut -d "/" -f2 | cut -d "/" -f1


mkdir ./client/profiles/<profile_name>/
cp -rf /opt/jans/jans-setup/output/test/jans-auth/client/* ./client/profiles/<profile_name>/
cp ./client/profiles/default/client_keystore.jks ./client/profiles/<profile_name>/

mkdir ./server/profiles/<profile_name>/
cp -rf /opt/jans/jans-setup/output/test/jans-auth/server/* ./server/profiles/<profile_name>/
cp ./server/profiles/default/client_keystore.jks ./server/profiles/<profile_name>/

```

### 4. This step is only needed if we need to run build and tests on another machine. Also its not needed if we deploy CA cert into Janssen instalation.

Import self signed http cert into java truststore.

Copy `/etc/certs/httpd.crt` from CE server to `<path>/httpd.crt` or run:
```
openssl s_client -connect <ce-server>:443 2>&1 |sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/httpd.crt
/opt/jre/bin/keytool -import -alias jans_http -keystore /opt/jre/lib/security/cacerts -file /tmp/httpd.crt
```
### 4.1. Import public cert into trustsore:
```
/opt/jre/bin/keytool -import -alias jans_http -keystore /opt/jre/lib/security/cacerts -file <path>/httpd.crt
```


### 5. Fill right configuration `cibaEndUserNotificationConfig`. It's in `jansConfDyn` in ou=jans-auth,ou=configuration,o=jans. After restart Jans Auth server:

```
systemctl restart jans-auth
```

### 6. Run build:
```
mvn -Dcfg=<profile_name> -Dcvss-score=9 -Dfindbugs.skip=true -Ddependency.check=false clean compile package
```
