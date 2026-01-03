Horizontal Scaling
Starting from version 5.4, it is possible to run several instances of Traccar in parallel to handle large number of devices. It was not possible on older versions because of an internal cache. There is still a cache, but it is simplified only stores data about connected devices and can be synchronized between distributed instances.

Traccar utilizes multicast messages for synchronization. You need to enable it in the configuration file like this:

<entry key='broadcast.interface'>en0</entry>
<entry key='broadcast.address'>230.3.3.3</entry>
<entry key='broadcast.port'>7001</entry>
Replace interface, address and port if needed to reflect your network setup.

In addition to the above, make sure all instances are configured to work with the same database.
