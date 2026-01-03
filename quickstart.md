Quick Start
This guide provides the initial steps to set up Traccar, login and connect your first device.

1 Download and Install
Start here if you plan to host your own instance of Traccar. If you're using one of our subscriptions, demo servers or already have Traccar installed and ready, skip to Point 2.

You can download an installer package for your operating system from the downloads page.

For the installation instructions check the corresponding section on our main documentation page.

2 Open the App
Open the server URL in a browser or use the Traccar Manager mobile app.

If you host your own server, the URL is http://localhost:8082 for a local connection or http://your.server.ip.address:8082 for an external access.

On some older version of Traccar, the default administrator account is created automatically with login admin and password admin. On newer versions there are no default users, so you have to register. First registered user automatically becomes an admin. If you're using a subscription, you will be provided with the URL and login details.

3 Register a Device
Click the add button (the plus icon) in the devices toolbar to register a new device.

You have to fill out the name and identifier fields. Name can be anything. The identifier has to match the unique id your device is reporting to the server. For most devices you should use IMEI or the serial number as the unique identifier.

If you don't know your device identifier, you can configure the device to report the data first and look for the id in the log. Check the troubleshooting guide for more details.

4 Configure the Device
Configure your device to start reporting the location data to your server. For more details on how to configure your specific model check your device manual or contact your device vendor support.

If you host your own server, your system must have a public IP address. Not all internet providers assign public IPs. Your current address appears to be 107.130.199.23, but it might not be your dedicated address if you are behind a NAT. If you don't have a public IP, you can use a VPS server or one of our subscriptions instead of hosting locally.

To select the correct port number, find your device in the list of supported devices. Port column indicates the default port number in Traccar for your device.
