Microsoft SQL Server
By default Traccar uses an embedded H2 database, but we don't recommend for it for production. If you want to use another SQL database instead, you can replace the following lines in the configuration file:

<entry key='database.driver'>org.h2.Driver</entry>
<entry key='database.url'>jdbc:h2:./data/database</entry>
<entry key='database.user'>sa</entry>
<entry key='database.password'></entry>
Configuration parameters for Microsoft SQL Server:

<entry key='database.driver'>com.microsoft.sqlserver.jdbc.SQLServerDriver</entry>
<entry key='database.url'>jdbc:sqlserver://[serverName][\instanceName];user=[userName];password=[password];databaseName=[database];trustServerCertificate=true;</entry>
<entry key='database.user'>[userName]</entry>
<entry key='database.password'>[password]</entry>
Where:

[serverName] - server name or address
[\instanceName] - instance (optional)
[userName] - database user name
[password] - user password
[database] - database name
It is also recommended to setup a job to clear history.
