---
layout: index
title: "Liberty Usage"
category: java
date: 2018-04-02 15:17:55
---

# Liberty Usage

## Download and install

1. [Download Liberty](https://developer.ibm.com/wasdev/downloads/liberty-profile-using-non-eclipse-environments/)

2. Unzip the file to the directory whatever you like.


## Create a server

```
cd path_to_liberty/wlp/bin

server create server_name  

```

## Edit the server.xml 

```
<?xml version="1.0" encoding="UTF-8"?>
<server description="new server">

    <!-- Enable features -->
    <featureManager>
        <feature>servlet-3.1</feature>
        <feature>ssl-1.0</feature>
        <feature>websocket-1.1</feature>
        <feature>jdbc-4.2</feature>
        <feature>jndi-1.0</feature>
    </featureManager>

    <!-- This template enables security. To get the full use of all the capabilities, a keystore and user registry are required. -->
    
    <!-- For the keystore, default keys are generated and stored in a keystore. To provide the keystore password, generate an 
         encoded password using bin/securityUtility encode and add it below in the password attribute of the keyStore element. 
         Then uncomment the keyStore element. -->
    <!--
    <keyStore password=""/> 
    -->
    <webContainer invokeFlushAfterService="false"/>
    
    <!--For a user registry configuration, configure your user registry. For example, configure a basic user registry using the
        basicRegistry element. Specify your own user name below in the name attribute of the user element. For the password, 
        generate an encoded password using bin/securityUtility encode and add it in the password attribute of the user element. 
        Then uncomment the user element. -->
    <basicRegistry id="basic" realm="BasicRealm"> 
        <!-- <user name="yourUserName" password="" />  --> 
    </basicRegistry>
    
    <!-- To access this server from a remote client add a host attribute to the following element, e.g. host="*" -->
    <httpEndpoint id="defaultHttpEndpoint"
				  host="*" httpPort="9080" httpsPort="9443" />
                  
    <!-- Automatically expand WAR files and EAR files -->
    <applicationManager autoExpand="true" startTimeout="360s" stopTimeout="120s"/> 
	  <application type="war" id="helloworld" name="helloworld" location="${server.config.dir}/apps/helloworld.war">
		  <classloader delegation="parentLast" />
    </application>
	
	<keyStore id="defaultKeyStore" password="admin" />

  <!-- Shared libraries
    https://www.ibm.com/support/knowledgecenter/SSEQTP_liberty/com.ibm.websphere.wlp.doc/ae/cwlp_sharedlibrary.html 
    -->
  <!-- JNDI data source confiduration -->
  <!-- Define a shared library pointing to the location of the JDBC driver JAR or compressed files. For example:  -->
  <library id="MySQLLib">
      <fileset dir="${shared.config.dir}/lib/global" includes="*.jar"/>
  </library>
  <!-- Configure attributes for the data source, such as JDBC vendor properties and connection pooling properties. For example:  -->
  <dataSource jndiName="jdbc/mysql" statementCacheSize="60" id="DefaultDataSource"
          isolationLevel="TRANSACTION_READ_COMMITTED" type="javax.sql.DataSource" transactional="true">
    <jdbcDriver libraryRef="MySQLLib"/>
    <properties databaseName="SAMPLEDB" 
                serverName="localhost" portNumber="3306" 
                user="root" password="mysqladmin"/>
  </dataSource>    

</server>
```

## Start/Stop the server

```
server start dev
server stop dev

```
