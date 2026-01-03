PostgreSQL
By default, Traccar uses an embedded H2 database, but we don't recommend it for production use. If you want to use another SQL database instead, replace the following lines in the configuration file:

<entry key='database.driver'>org.h2.Driver</entry>
<entry key='database.url'>jdbc:h2:./data/database</entry>
<entry key='database.user'>sa</entry>
<entry key='database.password'></entry>
Configuration parameters for PostgreSQL (replace [HOST], [DATABASE], [USER], and [PASSWORD] with the appropriate values; for a local database use localhost as the HOST):

<entry key='database.driver'>org.postgresql.Driver</entry>
<entry key='database.url'>jdbc:postgresql://[HOST]/[DATABASE]</entry>
<entry key='database.user'>[USER]</entry>
<entry key='database.password'>[PASSWORD]</entry>
TimescaleDB
TimescaleDB is an open-source time-series database. It is a PostgreSQL extension that optimizes storage and query performance for time-based data such as the location history in Traccar. We recommend using it for large-scale Traccar deployments.

The configuration is the same as the PostgreSQL sample above. If you want to deploy Traccar pre-configured with TimescaleDB, you can use this sample Docker Compose configuration:

Traccar with TimescaleDB
