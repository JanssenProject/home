# Setting up workspace for Janssen development

This is a step-by-step guide for developers and contributors to setup Janssen development environment on a personal workstation. Once setup, it will enable building and testing Janssen project components locally.

For the purpose of this guide, we are following steps and commands required 
on Ubuntu OS (version 18 or above). For all other OS platforms, like Windows, 
Mac, same steps and commands with platform specific changes can easily be derived.

- [Prepare LxC container](#Prepare-LxC-container)
- [Pre-Requisites](#pre-requisites)
- [Get Code](#get-code)
- [Setup Data Store](#setup-data-store)
- [Setup Configuration Files](#setup-configuration-files)
- [Setup HTTPS](#setup-https)
- [Build and Deploy](#build-and-deploy)
- [Run Tests](#run-tests)

## Prepare LxD container (applicable only to Ubuntu OS)

Commands below will help you to install LxD and create an Ubuntu container

- `sudo apt-get install lxd` - install LxD
- `systemctl start snap.lxd.daemon` - start LxD
- `lxd init` - initialize
- `sudo lxc launch images:ubuntu/focal/amd64 jans-dev` - create new container name `jans-dev` (can be anything) based on Ubuntu 20.4.
- `lxc exec jans-dev bash` - login to new container
- `sudo apt-get update` - update packages
- `sudo apt install git-all` - install git version control
- `sudo apt install maven` - install maven build tool
- `sudo apt install software-properties-common` - install basic software utilities

Steps in sections below are to be performed on container that you have created.


## Pre-requisites

### Download and Install required software

#### JDK 

For development as well as at runtime, Janssen requires any JDK with version 8 or above. 
Janssen in production environment uses Amazon Corretto (11.0.8) which is an OpenJDK 
distribution. Commands below will guide you through JDK installation:

```
wget -O- https://apt.corretto.aws/corretto.key | sudo apt-key add -
sudo add-apt-repository 'deb https://apt.corretto.aws stable main'
sudo apt-get update
sudo apt-get install -y java-11-amazon-corretto-jdk
java -version
```

#### MySQL

Janssen uses persistance storage to hold configuration and transactional data.
Janssen supports variety of persistance technologies including LDAP, RDBMS and cloud storage.
For this guide, we are going to use MySQL relational database as our persistance store.

```
sudo apt-get install mysql-server
```

#### Jetty server

Janssen uses Jetty as web application server. You can download and install Jetty 9 by using commands below:

```
wget https://repo1.maven.org/maven2/org/eclipse/jetty/jetty-distribution/9.4.44.v20210927/jetty-distribution-9.4.44.v20210927.zip
unzip jetty-distribution-9.4.44.v20210927.zip
mv jetty-distribution-9.4.44.v20210927/ jetty-home
```

### Setup environment variables

- Set `JAVA_HOME`: 
  This should be set so that it points
  to your JDK installation directory. You can set this in `bashrc` file on Ubuntu systems. Commands would be similar to:
  ```
  vim ~/.bashrc
  ```
  write line below at the end of bashrc:
  ```
  export JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto
  ```
  then run command below to load the variable in current bash
  ```
  . ~/.bashrc
  ```
    
- Set `JETTY_HOME`: 
  This is the directory where you have installed or unpacked your 
  jetty distribution. This directory should contain `start.jar` 
  so that `$JETTY_HOME/start.jar` is accessible. For example, line below should be added to bashrc like we did for `JAVA_HOME` :
  ```
  export JETTY_HOME=/root/jetty-home
  ```
  
- Set `JETTY_BASE`: 
  Set this variable by creating an empty directory 
  where you intend to deploy Janssen auth server web application. For example:
  ```
  mkdir ~/jetty-base
  ```
  then set this directory as jetty base 
  ```
  export JETTY_BASE=/root/jetty-base
  ```

- Set host name: 
  For Janssen modules to work correctly, you need to assign a
  host name to your local machine's IP. `localhost` is not 
  supported. To do this, we need to make changes to `hosts` file. On Ubuntu
  and other Linux destributions, this file is `/etc/hosts`, while for Windows
  same file can be found at `C:\Windows\System32\drivers\etc\hosts`. Make an 
  entry similar to below in `hosts` file:
  
  ```
  127.0.0.1       test.local.jans.io
  ```
  
  Here, `test.local.jans.io` can be any name of your choice. We will refer to 
`test.local.jans.io` as our host name for rest of this guide.

- Before we start, we need to initialize few Jetty modules as shown below:

  ```
  cd $JETTY_BASE
  
  java -jar $JETTY_HOME/start.jar --create-startd
  
  java -jar $JETTY_HOME/start.jar --add-to-start=server,deploy,annotations,resources,http-forwarded,threadpool,console-capture,logging-jetty,requestlog,jsp,websocket,https,ssl
  ```

## Get code

- JanssenProject code is hosted on Github. You can find it [here](https://github.com/JanssenProject). 
  Project has multiple repositories for various modules. This guide will take us
  through setup of Janssen Auth server module. For this, you need to clone repositories 
  listed below:
  
  1) [jans_setup](https://github.com/JanssenProject/jans-setup) (local clone location will be referred to as `setup-code-dir` in this guide)
  2) [jans-auth-sever](https://github.com/JanssenProject/jans-auth-server) (local clone location will be referred to as `auth-server-code-dir` in this guide)
    

## Setup Configuration Files

Janssen stores configuration required at the boot time in the file system. It is stored
at `/etc/jans/conf`. We need to create this directory on our local file system.

```
mkdir -p /etc/jans/conf
```

<!-- check if these steps for custom pages are needed separately or not. Custom
pages dir is empty plus it should ideally be part of war file-->

```
mkdir $JETTY_BASE/custom

mkdir $JETTY_BASE/custom/pages
```

Now, to jump start our configuration, we will use templates of configuration file provided in Janssen codebase. Let's copy the template files.

```
sudo cp <auth-server-code-dir>/server/target/conf/* /etc/jans/conf/
```

Now we need to edit configuration values for our environment.
Among copied files, there are two files that are notable:
  - `jans.properties`: Holds details like type of persistance to use, localtion of certificates etc.
  - `jans-sql.properties`: Since we are using MySQL RDBMS persistence store, 
    details in this file will be used to connect MySQL.
    
Edit values in both these files as recommended below.

```
sudo vim /etc/jans/conf/jans.properties
```

 -   edit `persistence.type: ldap` to `persistence.type: sql` since we are using MySQL as our backend
 -   edit `certsDir` to `certsDir=/etc/certs`
 -   edit value of `jansAuth_ConfigurationEntryDN` to `jansAuth_ConfigurationEntryDN=ou=jans-auth,ou=configuration,o=jans` 


```
sudo vim /etc/jans/conf/jans-sql.properties
```
 - Set `db.schema.name` to name of schema you created while importing data load script. 
 - Set `connection.uri` to `jdbc:mysql://localhost:3306/jansdb`
 - Set `auth.userName` to the new user that we created above, i.e `jans`
 - Set `auth.userPassword` to passwod that you set while creating new user, i.e `PassOfYourChoice`
 - Set `password.encryption.method` to method you have selected to encrypt the password for `userPassword` property. If you are using plain text password for your local setup, comment out this property.
    
 Properties of `jans-sql.properies` listed above are most likely to be customised as per your local setup. 
 Other properties from this file can be set to standard values as given below.

```
connection.driver-property.serverTimezone=UTC
# Prefix connection.driver-property.key=value will be coverterd to key=value JDBC driver properties
#connection.driver-property.driverProperty=driverPropertyValue

#connection.driver-property.useServerPrepStmts=false
connection.driver-property.cachePrepStmts=false
connection.driver-property.cacheResultSetMetadata=true
connection.driver-property.metadataCacheSize=500
#connection.driver-property.prepStmtCacheSize=500
#connection.driver-property.prepStmtCacheSqlLimit=1024

# Password hash method
password.encryption.method=SSHA-256

# Connection pool size
connection.pool.max-total=40
connection.pool.max-idle=15
connection.pool.min-idle=5

# Max time needed to create connection pool in milliseconds
connection.pool.create-max-wait-time-millis=20000

# Max wait 20 seconds
connection.pool.max-wait-time-millis=20000

# Allow to evict connection in pool after 30 minutes
connection.pool.min-evictable-idle-time-millis=1800000

binaryAttributes=objectGUID
certificateAttributes=userCertificate

```

## Setup SSL

- We will use Java keytool to generate key pair as given below.
    
```

keytool -genkeypair -alias jetty -keyalg EC -groupname secp256r1 -keypass secret -validity 3700 -storetype JKS -keystore keystore.test.local.jans.io.jks -storepass secret

What is your first and last name?
  [Unknown]:  test.local.jans.io
What is the name of your organizational unit?
  [Unknown]:  test.local.jans.io
What is the name of your organization?
  [Unknown]:  local.jans.io                                                         
What is the name of your City or Locality?
  [Unknown]:  <short name of your city>
What is the name of your State or Province?
  [Unknown]:  <short name of your state>
What is the two-letter country code for this unit?
  [Unknown]:  <short name of your country>



Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore keystore.test.local.jans.io.jks -destkeystore keystore.test.local.jans.io.jks -deststoretype pkcs12".

```

Above command will create a `.jks` file in the same directory from where you have executed the command.

Next, we will make changes in Jetty configuration to use the keystore.

   -   copy your keystore file which you generated above to `$JETTY_BASE/etc/`
        
        ```
        cp keystore.test.local.jans.io.jks $JETTY_BASE/etc/
        ```

   -   Now edit `ssl.ini` and change value of below properties 
        
       ```
       vim $JETTY_BASE/start.d/ssl.ini
       ```

   
       Uncomment and apply appropriate values to properties below according to inputs that you gave during creation of keystore.


       ```
       jetty.sslContext.keyStorePath=etc/keystore.test.local.jans.io.jks
       jetty.sslContext.keyStorePassword=secret
       jetty.sslContext.trustStorePath=etc/keystore.test.local.jans.io.jks
       jetty.sslContext.trustStorePassword=secret
       jetty.sslContext.keyManagerPassword=secret
       ```
    
- Add more keys to keystore. These keys are required for running tests.

   ```
   keytool -importkeystore -srckeystore <auth-server-code-dir>/server/profiles/default/client_keystore.jks -destkeystore keystore.test.local.jans.io.jks
   ```

- Generate JWT

   - download `jans-auth-client-1.0.0-SNAPSHOT-jar-with-dependencies.jar` from `https://maven.jans.io/maven/io/jans/jans-auth-client/1.0.0-SNAPSHOT/` to `/tmp/`
   - now run 
   
   ```
   java -Dlog4j.defaultInitOverride=true -cp /tmp/jans-auth-client-jar-with-dependencies.jar io.jans.as.client.util.KeyGenerator -keystore './keystore.test.local.jans.io.jks' -keypasswd secret -sig_keys RS256 RS384 RS512 ES256 ES384 ES512 -enc_keys RS256 RS384 RS512 ES256 ES384 ES512 -dnname 'CN=Jans Auth CA Certificates' -expiration 365 > /tmp/keys/keys_client_keystore.json
   ```
   
   This command adds additional keys in `keystore.test.local.jans.io.jks` and creates a JSON file with web keys. We will use web keys files later to update db entries.

- Provide keystore to Janssen and your code base. This is from where the tests are going to get executed.

   - copy keystore file to profiles in `client`, `server` modules in code base 
   
     - Client: 

     ```
     mkdir <auth-server-code-dir>/client/profiles/test.local.jans.io
     cp keystore.test.local.jans.io.jks <auth-server-code-dir>/client/profiles/test.local.jans.io/
     ```
     
     - Server: 
     
     ```
     mkdir <auth-server-code-dir>/server/profiles/test.local.jans.io
     cp keystore.test.local.jans.io.jks <auth-server-code-dir>/server/profiles/test.local.jans.io/
     ```

   - Copy keystore to Janssen
     
     ```
     mkdir /etc/certs
     
     sudo cp keystore.test.local.jans.io.jks /etc/certs/jans-auth-keys.jks
     
     sudo chown jetty:jetty  /etc/certs/jans-auth-keys.jks
     ```
  

## Setup data store

Janssen uses persistance storage to hold configuration and transactional data. 
Janssen supports variety of persistance mechanisms including LDAP, RDBMS and cloud storage.
For this guide, we are going to use MySQL relational database as a persistance store. 

As a first step, let's create schema and users.

- Log into MySQL using `root` user (or any other user with sufficient privileges)

  ```
  sudo mysql root
  ```
  
- Create new database(schema) for Janssen

  ```
  mysql> CREATE DATABASE jansdb;
  ```
  
- Create new db user `CREATE USER 'jans'@'localhost' IDENTIFIED BY 'PassOfYourChoice';`
- Grant privileges to new user on `jansdb` schema 
  ```
  GRANT ALL PRIVILEGES ON jansdb.* TO 'jans'@'localhost';
  ```
- Exit MySQL login 

Next, we will load basic configuration data into MySQL. This data is required
by Janssen modules at the time of start up. We will use a helper script that will create required tables and also insert basic configuration and test data.

- Get MySQL database data load script file from [here](TODO add link here). This 
script is a data dump which can be directly loaded into your local MySQL database. But before we run this generic script, we have to edit certain values as per our local setup.

##### Update hostname
we need to replace generic host name in the script with the one that we have set for our local environment, which is `test.local.jans.io`. To do that, open script in a text editor and
  
      ```
      TODO replace `testmysql.dd.jans.io` with actual host name from final script
      ```
  -   replace string `https://testmysql.dd.jans.io` with `https://test.local.jans.io:8443` 

  -   replace string `testmysql.dd.jans.io` with `test.local.jans.io`


##### Update keystore secret in database config

- update ` "keyStoreSecret": "<your-keystore-secret>",` value in script. This value is part of string value of `JansConfDyn` column in `jansAppConf` table. Here the `<key-store-secret>` is the secret you used while creating keystore in [Setup SSL](#setup-ssl) step.

- Script is now ready to be executed.

- Import data load script into your local MySQL

  ```
  sudo mysql -u root -p jansdb < jansdb_dump.sql;
  ```

##### Update JSON Web keys in database config


- open `/tmp/keys/keys_client_keystore.json` and copy content into db field `jansConfWebKeys` of `jansAppConf` table as shown below:

```
sudo mysql -u jans -p jansdb
```

```
UPDATE jansdb.jansAppConf SET jansConfWebKeys = '<multiline content from keystore json>' where doc_id = "jans-auth";
```

## Build and Deploy

```
cd <auth-server-code-dir>

mvn -DskipTests install
```
  
  This will create a `.war` file which we will use to deploy.
  
- Move and rename war `mv server/target/jans-auth-server.war $JETTY_BASE/webapps/jans-auth.war`
- Get `jans-auth.xml` file from [Github repo](https://github.com/JanssenProject/jans-setup/blob/master/templates/jetty/jans-auth.xml). Put this file under `$JETTY_BASE/webapps/`

```
wget https://raw.githubusercontent.com/JanssenProject/jans-setup/master/templates/jetty/jans-app.xml -P $JETTY_BASE/webapps/
```

TODO: file name has changed in latest git code, from jans-auth.xml to jans-app.xml. For now, I have renamed after downloading to jans-auth.xml

TODO: Needed to edit file at two places where variables were introduced. Not sure if this step was needed in the old jans-auth.xml as well or not. 


- Similarly, get `jans-auth_web_resources.xml` file from [Github repo](https://github.com/JanssenProject/jans-setup/blob/master/templates/jetty/jans-auth_web_resources.xml). Put this file under `$JETTY_BASE/webapps/`

   ```
   wget https://raw.githubusercontent.com/JanssenProject/jans-setup/master/templates/jetty/jans-auth_web_resources.xml -P $JETTY_BASE/webapps/
   ```

- To run Janssen auth server: 

  ```
  $ java -Djetty.home=$JETTY_HOME -Djetty.base=$JETTY_BASE -Dlog.base=/tmp/jans-logs -Djans.base=/etc/jans -Dserver.base=$JETTY_BASE -jar $JETTY_HOME/start.jar
  ```

At this point you should be able to get open id configuration JSON via:

```
curl -k https://10.229.38.155:8443/jans-auth/.well-known/openid-configuration
```

You can check logs at 

```
Console logs: /root/logs
Request logs: /root/jetty-base/logs/
Jans logs: <your log.base>/logs/
```

## Run Tests

Janssen integration tests need a Janssen server to execute successfully. Now that you have a Janssen instance running on your local machine, you can use it to run tests. We need to give our local workspace all the essential information about target Janssen server. This is configured in form of `profile`. Steps below will help us create profile in our local code workspace (`auth-server-code-dir`).

- Update your Java cacerts 

   This step is required in order to run tests from `client` module

  - extract certificate 
  
    ```
    openssl s_client -connect test.local.jans.io:8443 2>&1 |sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/httpd.crt
    ```
    this command takes few seconds to return.
  
  - Update cacerts of your JRE. For example if JRE being used my maven is `/usr/lib/jvm/java-11-amazon-corretto`. It will prompt for cert store password. Default is `changeit`.
  
    ```
    keytool -import -alias jetty -keystore /usr/lib/jvm/java-11-amazon-corretto/lib/security/cacerts -file /tmp/httpd.crt
    ``` 

- Create profile directory for client module `TODO: check if this step is required as this directory has been created in SSL setup`

  ```
  mkdir auth-server-code-dir/client/profiles/test.local.jans.io
  ```
  
- copy contents of default profile to new profile directory 

   ```
   cp ~/IdeaProjects/Janssen/jans-auth-server/client/profiles/default/* ~/IdeaProjects/Janssen/jans-auth-server/client/profiles/test.local.jans.io/
   ```
   `TODO: after this step, there are two .jks files in profile folder. Which one should we keep? keystore.test.local.jans.io.jks has all the keys from client_keystore.jks`
   
- Customize the values as per your setup
  
  ```
  cd ~/IdeaProjects/Janssen/jans-auth-server/client/profiles/test.local.jans.io/
  ```
  - In file `config-oxauth-test-data.properties` update values of `test.server.name` and `swd.resource` properties with your host name. i.e `test.local.jans.io` and update path in `clientKeyStoreFile` property with `profiles/test.local.jans.io/client_keystore.jks`

- create profile directory for server module `TODO: check if this step is required as this directory has been created in SSL setup`

  ```
  mkdir ~/IdeaProjects/Janssen/jans-auth-server/server/profiles/test.local.jans.io
  
  cp ~/IdeaProjects/Janssen/jans-auth-server/server/profiles/default/* ~/IdeaProjects/Janssen/jans-auth-server/server/profiles/test.local.jans.io/
  
  cd ~/IdeaProjects/Janssen/jans-auth-server/server/profiles/test.local.jans.io/
  ```
  
  - Edit config-oxauth.properties
    - Update values of `server.name`,`config.jans-auth.issuer`,`config.jans-auth.contextPath` properties to your host name `TODO: values of these properties should not be just server name in a dev setup, it should contain port number as well. Also for property config.jans-auth.contextPath, it should have /jans-auth after server and port as context
    - comment out the properties for LDAP
  - Edit config-oxauth-test.properties
    - update values of properties `server.name`,`config.oxauth.issuer`,`config.oxauth.contextPath` with your host name. Change `config.persistence.type` to `sql`. Remove all the properties till end of file and add properties mentioned below to the file with changes to mentioned properties:

    ```
    #sql
    config.sql.db.schema.name=gluudb

    config.sql.connection.uri=jdbc:mysql://test.dd.jans.io:3306/gluudb

    config.sql.connection.driver-property.serverTimezone=UTC
    # Prefix connection.driver-property.key=value will be coverterd to key=value JDBC driver properties
    #config.sql.connection.driver-property.driverProperty=driverPropertyValue

    config.sql.auth.userName=gluu
    config.sql.auth.userPassword=mXAkd11L8LSLXtT8bMPsBA==

    # Password hash method
    config.sql.password.encryption.method=SSHA-256

    # Connection pool size
    config.sql.connection.pool.max-total=20
    config.sql.connection.pool.max-idle=10
    config.sql.connection.pool.min-idle=5

    # Max time needed to create connection pool in milliseconds
    config.sql.connection.pool.create-max-wait-time-millis=20000

    # Max wait 20 seconds
    config.sql.connection.pool.max-wait-time-millis=20000

    # Allow to evict connection in pool after 30 minutes
    config.sql.connection.pool.min-evictable-idle-time-millis=1800000

    ```

- change values of property as below:

   ```
   config.sql.db.schema.name=<your schema>
   config.sql.connection.uri=jdbc:mysql://<your host>:3306/<your schema>
   config.sql.auth.userName=<db username>
   config.sql.auth.userPassword=<db plain text password>
   config.sql.password.encryption.method=<comment out this property>
   ```
- leave rest of the properties as they are
 
- Now you can run test cases for each module with
   
   ```
   mvn -Dcfg=test.dd.jans.io -fae -Dcvss-score=9 -Dfindbugs.skip=true -Dlog4j.default.log.level=TRACE -Ddependency.check=false clean test
   ```
- Edit Java files `TODO: this is a hack because we have `jans-auth` as web app context. In actual setup, there is no such context. So, in this setup, we need to have URLs with `jans-auth`. For now we are hard-coding in the code. Ideally, these steps should not be needed.
   - Edit `client/src/test/java/io/jans/as/client/BaseTest.java` where url `/.well-known/openid-configuration` is noted and add `jans-auth` to it so that it becomes `"/jans-auth/.well-known/openid-configuration"`
   - Similarly edit `client/src/main/java/io/jans/as/client/OpenIdConnectDiscoveryClient.java` file where URL `"/.well-known/webfinger"` is mentioned and make it `"/jans-auth/.well-known/webfinger"`


## Remote debug

Running Jans in lxc in debug mode and remotely debugging it from host machine

1)  In lxc container, run Jans using command like one below
    ```
    java -Djetty.home=$JETTY_HOME -Djetty.base=$JETTY_BASE -Dlog.base=/tmp/jans-logs -Djans.base=/etc/jans -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=5001 -Dserver.base=$JETTY_BASE -jar $JETTY_HOME/start.jar
    ```
2) On host machine, add a proxy device for port `5001` using command below
    ```
    lxc config device add jans-dev my-debug-port-5001 proxy listen=tcp:0.0.0.0:5001 connect=tcp:127.0.0.1:5001
    ```
3) Connect IDE on your host machine to Jans in LxC

- In IntellijIdea, 
  - `shift+shift` -> search for `edit configuration` -> click on `+` -> `remote jvm debugging` -> then give below values
  - `host:` you should give IP of `127.0.0.1`, `port:` 5001, `use module:` Keep default
  - Hit `apply` and `ok`
  - On top bar in intellij, you should be able to see name of your profile in a drop down. Hit the debug button on the same tool bar. You should see a debug view popping up and on console you should see a message like `Connected to the target VM, address: '127.0.0.1:5001', transport: 'socket'`. Click on `debugger` tab and then `threads` tab to see all the threads running. Also, at this point, you should be able to put breakpoint in the ide and debug process using that.

## Enable debug logging


## Troubleshooting steps:

- when running `client` module tests, if you get errors like `Failed to find private key`, check the keystore file `/etc/certs/jans-auth-keys.jks`. This sometimes gets overwritten when you stop jans server. You can check keys using 
  ```
  keytool -list -keystore /etc/certs/jans-auth-keys.jks
  ```
  when you look at it, you won't see all 18 keys. As a fix, you have to re-execute `Copy keystore to Janssen` step under SSL setup.
