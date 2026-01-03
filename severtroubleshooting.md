Server Troubleshooting
If you have problems with connecting your device to the server please follow the steps below.

1 Find HEX messages in the logs
Check tracking server logs:

On Linux and Mac it should be in /opt/traccar/logs
On Windows it's usually in C:\Program Files\Traccar\logs"
If server receives any data from device (even if the port is wrong), you should be able to see HEX messages in the log. If there are HEX messages in the log, go to point 2. If there are no HEX messages in the log, go to point 4.

2 Check if HEX messages are decoded
If there are HEX messages in the logs, you need to check following things:

If HEX messages are followed by Unknown device warning, then it means that you haven't registered device on the server. To fix this issue, add device with unique id you see in the log through web console.
If HEX messages are followed by INFO record with coordinates, then it means that data is decoded correctly. Possibly you need to wait a little bit for device to show up in the web console.
In any other case proceed to point 3.
3 Investigate why HEX messages are not decoded
There are two main possibilities:

Your device doesn't send GPS location. One of the reasons might be that it doesn't have a GPS fix. Note that GPS doesn't work indoors and it can take up to 15 or more minutes for device to get a first fix. Be patient.
You are using wrong port. Proceed to point 5.
4 Investigate why server does not receive data
If you don't see HEX message in the log, it means that server doesn't receive any data from your device. Possible reasons:

Server is not visible from the Internet. Use port check tool to verify. Proceed to point 6 if port is closed.
Device is configured incorrectly or there is some problem with the device. Contact device vendor for more information.
5 Find correct port for your device
Please read following information:

If you are using Traccar Client app, the port is always 5055.
If you are using a Chinese device, you must read about clones.
For all other devices, please read protocol identification guide.
If you were unable to identify port for your device, please contact support. You must provide provide protocol documentation and/or HEX message samples. Link to a store listing or user manual for a device is not sufficient.
6 Make sure required ports are open
First of all you should check if Traccar process is running and it's listening for ports locally. On Linux and Mac you can use netstat and ps command line tools. On Windows you can use Process Explorer.

If process is running and listening on ports, but port check tool still shows that ports are inaccessible, then there must be some network issue. It's impossible to cover every possible network problem, but here is a list of most common issues you might want to check for:

Router is not configured to forward external ports to your server.
Firewall on your server or router is blocking connections.
You do not have a public IP address. Contact your ISP or IT department to confirm.
