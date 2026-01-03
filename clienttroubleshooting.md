Traccar Client Troubleshooting
If you have problems with connecting Traccar Client app to the server please follow the steps below.

1 Check status screen
Check status screen in the Traccar Client app:

If you don't see "location update" messages proceed to point 2.
If you see "send error" messages proceed to point 3.
If you see location updates without errors, but no data on your server, it might mean that your device is configured with wrong server. Check app configuration.
If application stops working after some time check point 6.

2 Check accuracy parameter
If you don't see "location update" messages, it means that operating system doesn't provide app with location information.

If accuracy in the app is set to medium or low check point 4.
If accuracy in the app is set to high check point 5.
3 Check network and server
If you get "send error" messages, it means that server is not accessible or it doesn't accept your message. Possible reasons:

Your device id is not registered on the server. Make sure you use correct id on the server.
Network connection issues on your mobile device. Check network connectivity.
Wrong server URL details. Double-check app configuration.
Some other server issue. Try server troubleshooting guide.
4 Check network location provider
When medium (default) or low accuracy is selected in the app, it uses network location provider which determines location based on cell towers and wifi access points around device. Possible reasons for no location update:

Network location provider requires internet access. Check network connectivity.
Application doesn't have required permissions. Check system settings.
Location services are disabled completely. Check system settings.
On Android check that network location provider is enabled in system settings.
5 Check GPS location provider
When high accuracy is selected in the app, it uses GPS location provider which determines location based on signal for GPS satellites. Possible reasons for no location update:

No GPS signal. Remember that GPS fix takes time and only works with clear sky visivility. GPS does not work indoors.
Application doesn't have required permissions. Check system settings.
Location services are disabled completely. Check system settings.
On Android check that GPS location provider is enabled in system settings.
6 Common background issues
Both Apple and Google are trying to restrict background processes to give user better battery life, but it affects application like Traccar Client which require persistent background execution. Common issues to check for:

On iOS if you swipe app off the screen, operating system actually kills the process and app will no longer be able to report.
Make sure that in iOS settings background execution is allowed and Traccar Client has permission to always access location services.
On recent versions of Android make sure that you have added Traccar Client to battery optimization exceptions.
Some Android vendors have their own battery optimization in addition to standard Android system. Make sure those are disabled or the app is added to exceptions.
Check Don't kill my app website for more vendor specific details.
If you still have issues, please collect operating system logs.
