MySQL Database
By default, Traccar uses an embedded H2 database, but we don't recommend it for production use. If you want to use another SQL database instead, replace the following lines in the configuration file:

<entry key='database.driver'>org.h2.Driver</entry>
<entry key='database.url'>jdbc:h2:./data/database</entry>
<entry key='database.user'>sa</entry>
<entry key='database.password'></entry>
Configuration parameters for MySQL (replace [HOST], [DATABASE], [USER], and [PASSWORD] with the appropriate values; for a local database use localhost as the HOST):

<entry key='database.driver'>com.mysql.cj.jdbc.Driver</entry>
<entry key='database.url'>jdbc:mysql://[HOST]/[DATABASE]?zeroDateTimeBehavior=round&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true&amp;useSSL=false&amp;allowMultiQueries=true&amp;autoReconnect=true&amp;useUnicode=yes&amp;characterEncoding=UTF-8&amp;sessionVariables=sql_mode=''</entry>
<entry key='database.user'>[USER]</entry>
<entry key='database.password'>[PASSWORD]</entry>
Traccar will create the tables for you, but not the database itself. Ensure that the database uses the InnoDB engine, as Traccar relies on foreign-key constraints, which the MyISAM engine does not support.
We also recommend checking the following pages:

MySQL optimization guide
Clear history guide
If you want to deploy Traccar pre-configured with MySQL, you can use this sample Docker Compose configuration:

Traccar with MySQL
