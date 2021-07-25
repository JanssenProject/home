# Setting up workspace Janssen development

This is a step-by-step guide for developers and contributors to setup development environment on a personal workstation. Once setup, it will enable building and testing Janssen project components locally.

For the purpose of this guide, we are following steps and commands required 
on Ubuntu OS (version 18 or above). For all other OS platforms, like Windows, 
Mac, same steps and commands with platform specific changes can easily be derived
to successfully setup Janssen modules.

- [Pre-Requisites](#pre-requisites)
- [Get Code](#get-code)
- [Setup Data Store](#setup-data-store)
- [Setup Configuration Files](#setup-configuration-files)
- [Setup HTTPS](#setup-https)
- [Build and Deploy](#build-and-deploy)

## Pre-requisites

### Download and Install required software

##### JDK 

For development as well as at runtime, Janssen requires any JDK version 8 or above. 
Janssen in production environment uses Amazon Corretto (11.0.8) which is an OpenJDK 
distribution. You can download it from [here](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/downloads-list.html).
   
   
##### Git

Janssen code is hosted on Github. You can use any Git client to interact with
repositories.
   

##### Maven

Maven is a build tool used by Janssen. You can download it from [here](https://maven.apache.org/download.cgi)


##### MySQL

Janssen uses persistance storage to hold configuration and transactional data.
Janssen supports variety of persistance technologies including LDAP, RDBMS and cloud storage technologies.
For this guide, we are going to use MySQL relational database as persistance store.
You can download and install MySQL from [here](https://www.mysql.com/downloads/)
   

##### Jetty server

Janssen uses Jetty as web application server. You can download and 
install Jetty 9 from [here](https://www.eclipse.org/jetty/download.php).

### Setup environment variables

- Set `JAVA_HOME`: This should be set so that it points
  to your JDK installation directory
- Set `JETTY_HOME`: This is the directory where you have installed or unpacked your 
  jetty distribution. This directory should contain `start.jar` 
  so that `$JETTY_HOME/start.jar` is accessible.
- Set `JETTY_BASE`: Set this variable by creating an empty directory 
  where you intend to deploy Janssen auth server web application
- Set host name: For Janssen modules to work correctly, you need to assign a
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

## Get code

- JanssenProject code is hosted on Github. You can find it [here](https://github.com/JanssenProject). 
  Project has multiple repositories for various modules. This guide will take us
  through setup of Janssen Auth server module. For this, you need to clone repositories 
  listed below:
  
  1) [jans_setup](https://github.com/JanssenProject/jans-setup) (local clone location will be referred to as `setup-code-dir` going forward)
  2) [jans-auth-sever](https://github.com/JanssenProject/jans-auth-server) (local clone location will be referred to as `auth-server-code-dir` going forward)
    
  
## Setup data store

Janssen uses persistance storage to hold configuration and transactional data. 
Janssen supports variety of persistance mechanisms including LDAP, RDBMS and cloud storage.
For this guide, we are going to use MySQL relational database as a persistance store. 

As a first step, we will load basic configuration data into MySQL. This data is required
by Janssen modules at the time of start up. This script will create required schema,
tables, users, permissions and also inserts basic configuration data in required tables.  

- Get MySQL database data load script file from [here](TODO add link here). This 
script is data dump which can directly be loaded in your local MySQL database. But
  before we load it, we need to replace generic host name in the script with the one
  that we have set for our local environment, which is `test.local.jans.io`. To do
  that, open script in any text editor and
  
    -   replace string `https://testmysql.dd.jans.io` with `https://test.local.jans.io:8443` 
  <!-- TODO replace `testmysql.dd.jans.io` with actual host name from final script -->  
    -   replace string `testmysql.dd.jans.io` with `test.local.jans.io`

 
- Import data load script into your local MySQL
`mysql -u root -p jansdb < jansdb_dump.sql`

Now log into MySQL using `root` user (or any other user with sufficient privileges)

- Create new db user `CREATE USER 'jans'@'localhost' IDENTIFIED BY 'PassOfYourChoice';`
- Grant privileges to new user on `jans` schema `GRANT ALL PRIVILEGES ON jansdb.* TO 'jans'@'localhost';`

## Setup Configuration Files

Janssen stores configurationrequired at the boot time, in file system. It is stored
at `/etc/jans/conf`. We need to create that directory on our local file system.

- `sudo mkdir /etc/jans`

- `sudo mkdir /etc/jans/conf`

<!-- check if these steps for custom pages are needed separately or not. Custom
pages dir is empty plus it should ideally be part of war file-->
- `mkdir $JETTY_BASE/custom`

- `mkdir $JETTY_BASE/custom/pages`

Now, we need to copy teplates of these configuration files from our code base.

- `sudo cp /home/dhaval/eclipse-workspace/Janssen/jans-auth-server/server/target/conf/* /etc/jans/conf/`

Among copied files, there are two files that are notable:
  - `jans.properties`: Holds details like type of persistance to use, DB 
  entry from where further configuration should be loaded, localtion of certificates etc.
  - `jans-sql.properties`: Since we are using MySQL which is RDBMS store as persistence store, 
    details in this file will be used to connect and configure connection between
    Janssen and MySQL.
    
Edit values in both these files as recommended below.

- `sudo vim /etc/jans/conf/jans.properties`

    -   edit `persistence.type: ldap` to `persistence.type: sql`
    -   edit `certsDir` to `certsDir=/etc/jans/conf`
    -   edit value of `jansAuth_ConfigurationEntryDN` to `jansAuth_ConfigurationEntryDN=ou=jans-auth,ou=configuration,o=jans` so that it matches `jans-auth` entry in `jansAppConf` table in DB   
    -   since this file taken from source, it is different when compared to file from deployed instance. You will see multiple entries for different modules similar to `jansAuth_ConfigurationEntryDN`. I think these entries are added as installed instances deploy other modules too, like scim, jans conf api etc.

- `sudo vim /etc/jans/conf/jans-sql.properties`
    - Set `db.schema.name` to name of schema you created while importing data load script. 
    - Set `connection.uri` to `jdbc:mysql://localhost:3306/jansdb`
    - Set `auth.userName` to the new user that we created above, i.e `jans`
    - Set `auth.userPassword` to passwod that you set while creating new user, i.e `PassOfYourChoice`
    - Set `password.encryption.method` to method you have selected to encrypt the password for `userPassword` property. If you are using plain text password for your local setup, comment out this property.
    
    Properties of `jans-sql.properies` listed above are most likely to be customised as per your local setup. 
    Other properties from this file can be set to values as given below.

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

## Setup HTTPS

Janssen uses secure socket layer (SSL) to secure HTTP communication. To enable 
same of our local setup, we need to configure self signed ceritificates. 

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
        
        `cp keystore.test.local.jans.io.jks $JETTY_BASE/etc/`

-   Now edit `ssl.ini` and change value of below properties 
        
    `vim $JETTY_BASE/start.d/ssl.ini`

   
    Uncomment and apply appropriate values to properties below according to inputs that you gave during creation of keystore.

   
    `jetty.sslContext.keyStorePath=etc/keystore.test.local.jans.io.jks`

    `jetty.sslContext.keyStorePassword=secret`

    `jetty.sslContext.trustStorePath=etc/keystore.test.local.jans.io.jks`

    `jetty.sslContext.trustStorePassword=secret`

    `jetty.sslContext.keyManagerPassword=secret`

## Build and Deploy

- `cd auth-server-code-dir`
- `mvn -DskilTests install` 
  
  This will create a `.war` file which we will use to deploy.
  
- Move and rename war `mv server/target/jans-auth-server.war $JETTY_BASE/webapps/jans-auth.war`
- Get `jans-auth.xml` file from [Github repo](https://github.com/JanssenProject/jans-setup/blob/master/templates/jetty/jans-auth.xml). Put this file under `$JETTY_BASE/webapps/`
- Similarly, get `jans-auth_web_resources.xml` file from [Github repo](https://github.com/JanssenProject/jans-setup/blob/master/templates/jetty/jans-auth_web_resources.xml). Put this file under `$JETTY_BASE/webapps/`
- To run Janssen auth server: 
  ```
  $ java -Djetty.home=$JETTY_HOME -Djetty.base=$JETTY_BASE -Dlog.base=/home/dhaval/temp/jetty-logs -Djans.base=/etc/jans -Dserver.base=$JETTY_BASE -jar $JETTY_HOME/start.jar
  ```


