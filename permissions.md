Permissions
Almost all entities in Traccar have associated permissions and can be linked to user accounts. It applies not only to devices, but also things like geofences, notifications etc.

Administrators and managers can control permissions for their users from the Users menu in the settings. It is accessible from the Connections menu option in the users list.

When an object is shared between several users, they have full access to it, including the ability to delete it. If you want to avoid it, you can duplicate instances so that each user has their own object. Duplicating a device with the same unique id is not possible, so Traccar provides a special device readonly user setting to restrict users for modifying devices.

Devices
Some entities can be linked to devices. For example, a geofence or a notification can be linked to a device. When linked, it means that it's associated with the device, but it does not affect user permissions. To provide user access, it also needs to be linked to the user account.

For example, if a geofence is linked to a device, it means that the geofence events will be generated for this device.

Linking can be done from the settings. It is accessible from the Connections menu option in the devices list.

Groups
A group represent a group of devices. It is not possible to group any other entities, but you can link some entities to groups the same way you link them to devices. Same as with devices, linking to a group does not affect user permissions.

For example, if a geofence is linked to a group, it means that the geofence events will be generated for all devices in the group and subgroups.

Linking can be done from the settings. It is accessible from the Connections menu option in the groups list.

To add a device to a group, edit the device and select the Group under the Extra section. Groups can be nested. To add a group to another group, edit the group and select the parent Group under the Extra section.
