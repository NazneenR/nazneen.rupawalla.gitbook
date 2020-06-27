---
description: >-
  Steps to get around the errors during setup. Old blog written on blogger,
  rewritten here
---

# Setting up MySQL

The tech stack for my B.E project included MySQL.  
When I started out, faced a number of issues while trying to get it working.  
Felt the need to collect all the steps at one place, to solve the errors when someone tries to work with MySQL for the first time.

Following are the steps:  
After you download **MySQL and MySQL Connector/J**, following are the errors you may came across and the steps to solve it:

* **`java.lang.ClassNotFoundException: com.mysql.jdbc.Driver`** \`\` The error is self explanatory. The **MySQL JDBC Driver cannot** be found. You may set the classpath environment variable for connector/J but it will not work.  After reading the solution [here](http://stackoverflow.com/questions/2591505/java-lang-classnotfoundexception-com-mysql-jdbc-driver), I understood that the environment variable is considered a poor practice and the Classpath environment variable is only used by the java.exe command. This variable is ignored by IDEs. **The classpath has to be specified in the Classpath entry of JAR's own /META-INF/MANIFEST.MF file.** The path can be relative to the JAR itself- It can be in the same folder or in a /lib subfolder.  The below example assumes the driver to be in the same folder as the JAR. **Class-Path: mysql-connector-java.jar** For more- [http://stackoverflow.com/questions/8621141/java-lang-classnotfoundexception-com-mysql-jdbc-driver-when-executing-jar](http://stackoverflow.com/questions/8621141/java-lang-classnotfoundexception-com-mysql-jdbc-driver-when-executing-jar) 
* When you try to run your app, once your code is in place to connect to the DB, you may get this exception **`com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Access denied for user 'username'@'%' to database 'db'`** Links to understand how to avoid the error:
  * [http://dev.mysql.com/doc/refman/5.6/en/access-denied.html](http://dev.mysql.com/doc/refman/5.6/en/access-denied.html)  
  * [http://dev.mysql.com/doc/refman/5.1/en/adding-users.html](http://dev.mysql.com/doc/refman/5.1/en/adding-users.html)  
    The above steps should help you avoid the following error, too:  
    **`Host 'MachineName' is not allowed to connect to this MySql Server`**

    By this command, you should be able to get it working.

    **`GRANT ALL PRIVILEGES ON *.* TO root@'hostname' IDENTIFIED BY 'root-password'`**

Things should work fine now.  
Lastly, the following link will help you with all the needed commands:  
[http://www.pantz.org/software/mysql/mysqlcommands.html](http://www.pantz.org/software/mysql/mysqlcommands.html)  
Hope the above steps helped.

Happy Coding..:\)

