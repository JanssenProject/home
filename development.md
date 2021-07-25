# Setting up workspace Janssen development

## Pre-requisites

### Download and Install required software

1) JDK 
   
   For development as well as at runtime, Janssen requires any JDK version 8 or above. 
   Janssen in production environment uses Amazon Corretto (11.0.8) which is an OpenJDK 
   distribution. You can download it from [here](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/downloads-list.html).
   
   
2) Git
   
    Janssen code is hosted on Github. You can use any Git client to interact with
    repositories.
   

3) Maven
   
    Maven is a build tool used by Janssen. You can download it from [here](https://maven.apache.org/download.cgi)


4) MySQL

   Janssen uses persistance storage to hold configuration and transactional data.
   Janssen supports variety of persistance technologies including LDAP, RDBMS and cloud storage technologies.
   For this guide, we are going to use MySQL relational database as persistance store.
   You can download and install MySQL from [here](https://www.mysql.com/downloads/)
   

5) Jetty server
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

## Steps:

Now that our environment is ready, we will the steps below to bring up 
Janssen auth server module in our local environment.

1) Build code
2) Setup data store 
4) Setup webserver and deploy 
5) Execute test cases

For the purpose of this guide, we are following steps and commands required 
on Ubuntu OS (version 18 or above). For all other OS platforms, like Windows, 
Mac, same steps and commands with platform specific changes can easily be derived
to successfully setup Janssen modules.

### Build code

- JanssenProject code is hosted on Github. You can find it [here](https://github.com/JanssenProject). 
  Project has multiple repositories for various modules. This guide will take us
  through setup of Janssen Auth server module. For this, you need to clone repositories 
  listed below:
  
  1) [jans_setup](https://github.com/JanssenProject/jans-setup) (local clone location will be referred to as `setup-code-dir` going forward)
  2) [jans-auth-sever](https://github.com/JanssenProject/jans-auth-server) (local clone location will be referred to as `auth-server-code-dir` going forward)
    
- Execute `cd auth-server-code-dir`
- Execute `mvn -DskilTests install`

  Above command will give you a deployable `.war` file which we will use to
  deploy locally. `.war` file can be found at location below:

  `auth-server-code-dir/server/target/jans-auth-server.war`
  
  (We are skipping tests for now as tests need to run against a 
  deployed Janssen server which we don't have at this point. 
  We will run these tests later when we have our local deployment up and 
  running.) 
  
### Setup data store

Janssen uses persistance storage to hold configuration and transactional data. 
Janssen supports variety of persistance mechanisms including LDAP, RDBMS and cloud storage.
For this guide, we are going to use MySQL relational database as a persistance store. 

As a first step, we will load basic configuration data into MySQL. This data is required
by Janssen modules at the time of start up.
- Get MySQL database data load script file from [here](`TODO add link here`)
    -   replace `https://testmysql.dd.jans.io` with `http://localhost:8080`
    -   replace `testmysql.dd.jans.io` with `localhost`

 

`mysql -u root -p gluudb < /home/dhaval/Downloads/gluudb_dump.sql`

You can get `gluudb_dump.sql` as download from this link `TODO`.

This command will create a new schema called `gluudb` in your mysql and load it with data required to bootstrap Janssen server.

Now log into mysql using `root` mysql user (or any other user with sufficient privs)

- Create new db user `CREATE USER 'gluu'@'localhost' IDENTIFIED BY 'gluupass';`
- Grant privs to new user `GRANT ALL PRIVILEGES ON gluudb.* TO 'gluu'@'localhost';`

### setup properties files and certificates

- `sudo mkdir /etc/jans`

- `sudo mkdir /etc/jans/conf`

- `mkdir $JETTY_BASE/custom`

- `mkdir $JETTY_BASE/custom/pages`

- `sudo cp /home/dhaval/eclipse-workspace/Janssen/jans-auth-server/server/target/conf/* /etc/jans/conf/`

- `sudo vim /etc/jans/conf/jans.properties`

    -   edit `persistence.type: ldap` to `persistence.type: sql`
    -   edit `certsDir` to `certsDir=/etc/jans/conf`
    -   edit value of `jansAuth_ConfigurationEntryDN` to `jansAuth_ConfigurationEntryDN=ou=jans-auth,ou=configuration,o=jans` so that it matches `jans-auth` entry in `jansAppConf` table in DB   
    -   since this file taken from source, it is different when compared to file from deployed instance. You will see multiple entries for different modules similar to `jansAuth_ConfigurationEntryDN`. I think these entries are added as installed instances deploy other modules too, like scim, jans conf api etc.

- `sudo vim /etc/jans/conf/jans-sql.properties`

change values in this file, which looks like below at the start, to suit to your setup. Look at the example values given below:

Changes are required to values below :
- db.schema.name
- connection.uri
- auth.userName
- auth.userPassword
- password.encryption.method (comment this out)

change other property values same as in the sample

```

db.schema.name=${config.sql.db.schema.name}

connection.uri=${config.sql.connection.uri}

connection.driver-property.serverTimezone=${config.sql.connection.driver-property.serverTimezone}

auth.userName=${config.sql.auth.userName}
auth.userPassword=${config.sql.auth.userPassword}

# Password hash method
password.encryption.method=${config.sql.password.encryption.method}

# Connection pool size
connection.pool.max-total=${config.sql.connection.pool.max-total}
connection.pool.max-idle=${config.sql.connection.pool.max-idle}
connection.pool.min-idle=${config.sql.connection.pool.min-idle}

# Max time needed to create connection pool in milliseconds
connection.pool.create-max-wait-time-millis=${config.sql.connection.pool.create-max-wait-time-millis}

# Max wait 20 seconds
connection.pool.max-wait-time-millis=${config.sql.connection.pool.max-wait-time-millis}

# Allow to evict connection in pool after 30 minutes
connection.pool.min-evictable-idle-time-millis=${config.sql.connection.pool.min-evictable-idle-time-millis}


```

- As in the sample below 

```

db.schema.name=gluudb

connection.uri=jdbc:mysql://localhost:3306/gluudb

connection.driver-property.serverTimezone=UTC
# Prefix connection.driver-property.key=value will be coverterd to key=value JDBC driver properties
#connection.driver-property.driverProperty=driverPropertyValue

#connection.driver-property.useServerPrepStmts=false
connection.driver-property.cachePrepStmts=false
connection.driver-property.cacheResultSetMetadata=true
connection.driver-property.metadataCacheSize=500
#connection.driver-property.prepStmtCacheSize=500
#connection.driver-property.prepStmtCacheSqlLimit=1024

auth.userName=gluu
auth.userPassword=SbDSfEKpR5JeO/iswwhNkw==

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


#### setup certificates

- give a domain name to your localhost in your `hosts` file
    - eg: `127.0.0.1    test.local.jans.io`
- create key using keytool
    
    ```

dhaval@thinkpad:~/temp/keys$ keytool -genkeypair -alias jetty -keyalg EC -groupname secp256r1 -keypass secret -validity 3700 -storetype JKS -keystore keystore.test.local.jans.io.jks -storepass secret
What is your first and last name?
  [Unknown]:  test.local.jans.io
What is the name of your organizational unit?
  [Unknown]:  test.local.jans.io
What is the name of your organization?
  [Unknown]:  local.jans.io                                                         
What is the name of your City or Locality?
  [Unknown]:  ahm
What is the name of your State or Province?
  [Unknown]:  gj
What is the two-letter country code for this unit?
  [Unknown]:  in
Is CN=test.local.jans.io, OU=test.local.jans.io, O=local.jans.io, L=ahm, ST=gj, C=in correct?
  [no]:  yes


Warning:
The JKS keystore uses a proprietary format. It is recommended to migrate to PKCS12 which is an industry standard format using "keytool -importkeystore -srckeystore keystore.test.local.jans.io.jks -destkeystore keystore.test.local.jans.io.jks -deststoretype pkcs12".


    ```


- Make changes in Jetty configuration to use the keystore

    -   copy your keystore file which you generated above to `$JETTY_BASE/etc/`
        
        `cp ~/temp/keys/keystore.test.local.jans.io.jks $JETTY_BASE/etc/`

    -   Now edit `ssl.ini` and change value of below properties 
        
        `vim $JETTY_BASE/start.d/ssl.ini`

        Uncomment and apply appropriate values to properties below according to inputs that you gave during creation of keystore.

        `jetty.sslContext.keyStorePath=etc/keystore.test.local.jans.io.jks`

        `jetty.sslContext.keyStorePassword=secret`

        `jetty.sslContext.trustStorePath=etc/keystore.test.local.jans.io.jks`

        `jetty.sslContext.trustStorePassword=secret`

        `jetty.sslContext.keyManagerPassword=secret`






### Build and deploy Janssen auth server

- go to the directory where you have downloaded code for `jans-auth-server`
- `cd jans-auth-server`
- `mvn -DskilTests install`
- move and rename war `mv server/target/jans-auth-server.war $JETTY_BASE/webapps/jans-auth.war`
- Get `jans-auth.xml` file from [Github repo](https://github.com/JanssenProject/jans-setup/blob/master/templates/jetty/jans-auth.xml). Put this file under `$JETTY_BASE/webapps/`
- similarly, get `jans-auth_web_resources.xml` file from [Github repo](https://github.com/JanssenProject/jans-setup/blob/master/templates/jetty/jans-auth_web_resources.xml). Put this file under `$JETTY_BASE/webapps/`
- To run Janssen auth server: 
  ```
  $ java -Djetty.home=$JETTY_HOME -Djetty.base=$JETTY_BASE -Dlog.base=/home/dhaval/temp/jetty-logs -Djans.base=/etc/jans -Dserver.base=$JETTY_BASE -jar $JETTY_HOME/start.jar
  ```


