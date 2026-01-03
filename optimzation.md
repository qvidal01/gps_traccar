Optimization
This page provides information on configuring Traccar and operating system to work with a large number of devices and users.

In addition to the server optimization, we recommend a regular clean up of old location history and logs. Check clear history page for more details on how to configure it.

Increasing connection limit on Linux
You can check hard and soft limits for current user using the following command:

cat /proc/$(pidof java)/limits | grep "open files"
On most systems by default the limit is 1024 connections.

To increase the limit add the following lines to /etc/security/limits.conf:

* soft  nofile 50000
* hard  nofile 50000
On some versions of Ubuntu there is an issue and you need to do the following:

Edit /etc/systemd/user.conf and add DefaultLimitNOFILE=50000
Edit /etc/systemd/system.conf and add DefaultLimitNOFILE=50000
Make sure you use a number higher than your number of devices because when device reconnects it might consume two or even more connection for some period of time.

System restart is required for the chagnes to take affect.

Scaling beyond 65k connections
After following instructions above you might find that connection limit doesn't increase past 65k or even smaller number. This is because of the vm.max_map_count variable.

To fix the issue you need to modify "/etc/sysctl.conf":

vm.max_map_count = 250000
fs.file-max = 250000
net.ipv4.ip_local_port_range = 1024 65535
The first two parameters allow to increase total limit of connections to 250k. The last parameter is important to increase the local port range, which would allow more connections on a single port. By default the range is usually around 32k.

Don't forget to restart the system after modifying the file.

Setting connection timeout in Traccar
Operating systems have a timeout for all TCP connections, but it's usually very high. For example, on Linux it's common to have a 2 hours timeout by default. It means that if your device re-connects without gracefully closing connection, then it will leave a stale connection on the server that consumes server resources and is counted against the total connection limit. When network connection is poor, a single device can easily create tens or even hundreds of connections within 2 hour period.

To avoid the problem, it is recommended to set the connection timeout in Traccar server. You can use server.timeout or protocol.timeout option in the config file. It is recommended to set the value (in seconds) to slightly higher than your device reporting interval.

Service configuration parameters
Traccar uses Java virtual machine, so it has restrictions on the amount of memory it can use on the system.

You can change Java heap size by adding following parameters to the service config:

-Xmx to specify the maximum heap size
-Xms to specify the initial Java heap size
On Linux the service config located at /etc/systemd/system/traccar.service:

ExecStart=/opt/traccar/jre/bin/java -Xmx1G -jar tracker-server.jar conf/traccar.xml
In above example the maximum heap size is set to 1GB.

Selecting database engine
By default Traccar uses embedded H2 database system. It's used to simplify initial set up and configuration of the server software, but for any production environment it's strongly recommended to use a fully-featured database engine. One of the best results in terms of performance are observed with MySQL database. Traccar also supports other popular database systems (Microsoft SQL Server, PostgreSQL and others).

Make sure that the database is configured appropriately to handle the amount of data and traffic you plan to have. Default cache size and other configuration parameters might not be the best for your use case. For MySQL tips you can check our MySQL optimization documentation page.
