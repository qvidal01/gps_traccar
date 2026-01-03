Geofences
Geofences are geographical zones that can be used to generate events about device movement in and out of the zone.

There are three supported geometry types:

Polygon

Polyline

Circle

Permissions
If a geofence is linked to a device, it means that the system will monitor device entering and exiting that geofence.

If a geofence is linked to a group, it means that the system will monitor all devices in that group entering and exiting the geofence.

If a user has access to a geofence, it means that the user can edit/remove the geofence and can subscribe to the geofence events.

Calendars can be linked to geofences. It will limit events generation to the calendar schedule.

Other Information
Distance around a polyline geofence, when device is considered inside the geofence, can be configured with the geofence.polylineDistance configuration parameter (25 meters by default).

The color of a geofence on the map can be set in the geofence attribute color. Any valid HTML colors are accepted.


If you need control device movement along a predefined route, you can use a polyline geofence. Also, if you need control when a device started and ended the route, you can add additional geofences at the start and at the end.

