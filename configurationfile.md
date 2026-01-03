Configuration File
Most global server parameters are defined in the XML configuration file. On Linux, the file is located at /opt/traccar/conf/. On Windows, the location can be set during installation, but by default it is C:\Program Files\Traccar\conf. You can edit this file with any text editor—just ensure it remains valid XML and that special characters are properly escaped.

Some parameters can be configured as attributes on Device, Server, and other objects through the web app. Refer to the badge next to each attribute key to see where a parameter can be used.

For Boolean settings, any value left unspecified is interpreted by the server as false.

You can also override configuration values with environment variables, which take precedence over the XML file. To enable this, set CONFIG_USE_ENVIRONMENT_VARIABLES to true. To convert config key to an environment variable key replace dots with underscore and convert the rest to screaming snake case. For example, mail.smtp.fromName will be MAIL_SMTP_FROM_NAME.

List of available configuration parameters:

[protocol].address config
Network interface for the protocol. If not specified, server will bind all interfaces.

[protocol].port config
Port number for the protocol. Most protocols use TCP on the transport layer. Some protocols use UDP. Some support both TCP and UDP.

[protocol].devices config
List of devices for polling protocols. List should contain unique ids separated by commas. Used only for polling protocols.

[protocol].interval config
Polling interval in seconds. Used only for polling protocols.

[protocol].ssl config
Enable SSL support for the protocol. Not all protocols support this.

[protocol].timeout config
Connection timeout value in seconds. Because sometimes there is no way to detect lost TCP connection old connections stay in open state. On most systems there is a limit on number of open connection, so this leads to problems with establishing new connections when number of devices is high or devices data connections are unstable.

devicePassword device
Device password. Commonly used in some protocol for sending commands.

[protocol].devicePassword config
Device password. Commonly used in some protocol for sending commands.

[protocol].mask config
Default protocol mask to use. Currently used only by Skypatrol protocol.

[protocol].messageLength config
Custom message length. Currently used only by H2 protocol for specifying binary message length.

[protocol].extended config
Enable extended functionality for the protocol. The reason it's disabled by default is that not all devices support it.

[protocol].utf8 config
Decode string as UTF8 instead of ASCII. Only applicable for some protocols.

[protocol].can config
Enable CAN decoding for the protocol. Similar to 'extended' configuration, it's not supported for some devices.

[protocol].ack config device
Indicates whether server acknowledgement is required. Only applicable for some protocols.

Default value: false

[protocol].ignoreFixTime config
Ignore device reported fix time. Useful in case some devices report invalid time. Currently only available for GL200 protocol.

[protocol].decodeLow config
Decode additional TK103 attributes. Not supported for some devices.

[protocol].longDate config
Use long date format for Atrack protocol.

[protocol].decimalFuel config
Use decimal fuel value format for Atrack protocol.

[protocol].custom config
Indicates additional custom attributes for Atrack protocol.

[protocol].form config
Custom format string for Atrack protocol.

[protocol].frameMask config
Frame mask for Atrack protocol.

[protocol].config config
Protocol configuration. Required for some devices for decoding incoming data.

[protocol].alarmMap config
Alarm mapping for Atrack protocol.

[protocol].prefix config
Indicates whether TAIP protocol should have prefixes for messages.

[protocol].server config
Some devices require server address confirmation. Use this parameter to configure correct public address.

suntech.protocolType config device
Protocol type for Suntech.

suntech.hbm config device
Suntech HBM configuration value.

[protocol].includeAdc config device
Format includes ADC value.

[protocol].includeRpm config device
Format includes RPM value.

[protocol].includeTemp config device
Format includes temperature values.

[protocol].disableCommands config
Disable commands for the protocol. Not all protocols support this option.

[protocol].format config device
Protocol format. Used by protocols that have configurable message format.

[protocol].dateFormat device
Protocol date format. Used by protocols that have configurable date format.

decoder.timezone config device
Device time zone. Most devices report UTC time, but in some cases devices report local time, so this parameter needs to be configured for the server to be able to decode the time correctly.

orbcomm.accessId config
ORBCOMM API access id.

orbcomm.password config
ORBCOMM API password.

[protocol].alternative config device
Use alternative format for the protocol of commands.

Default value: false

[protocol].language config device
Protocol format includes a language field.

Default value: false

server.buffering.threshold config
If not zero, enable buffering of incoming data to handle ordering locations. The value is threshold for buffering in milliseconds.

Default value: 3000L

server.timeout config
Server wide connection timeout value in seconds. See protocol timeout for more information.

server.delayAcknowledgement config
Send device responses immediately before writing it in the database.

server.nettyBossThreads config
Number of Netty boss threads. If not specified or zero, Netty default value is used.

Default value: 0

server.nettyThreads config
Number of Netty worker threads. If not specified or zero, Netty default value is used.

Default value: 0

server.statistics config
Address for uploading aggregated anonymous usage statistics. Uploaded information is the same you can see on the statistics screen in the web app. It does not include any sensitive (e.g. locations).

Default value: "https://www.traccar.org/analytics/"

fuelDropThreshold server device
Fuel drop threshold value. When fuel level drops from one position to another for more the value, an event is generated.

Default value: 0.0

fuelIncreaseThreshold server device
Fuel increase threshold value. When fuel level increases from one position to another for more the value, an event is generated.

Default value: 0.0

fuelCapacity device
Device fuel tank capacity in liters.

speedLimit server device
Speed limit value in knots.

Default value: 0.0

disableShare server
Disable device sharing on the server.

event.overspeed.thresholdMultiplier config
Speed limit threshold multiplier. For example, if the speed limit is 100, but we only want to generate an event if the speed is higher than 105, this parameter can be set to 1.05. Default multiplier is 1.0.

Default value: 1.0

event.overspeed.minimalDuration config
Minimal over speed duration to trigger the event. Value in seconds.

event.overspeed.preferLowest config
Relevant only for geofence speed limits. Use the lowest speed limit from all geofences.

event.behavior.accelerationThreshold config
Driver behavior acceleration threshold. Value is in meter per second squared.

event.behavior.brakingThreshold config
Driver behavior braking threshold. Value is in meter per second squared.

event.ignoreDuplicateAlerts config
Do not generate alert event if same alert was present in last known location.

Default value: true

event.motion.speedThreshold config device
If the speed is above specified value, the object is considered to be in motion. Default value is 0.01 knots.

Default value: 0.01

database.memory config
Enable in-memory database instead of an SQL database.

database.driverFile config
Path to the database driver JAR file. Traccar includes drivers for MySQL, PostgreSQL and H2 databases. If you use one of those, you don't need to specify this parameter.

database.driver config
Database driver Java class. For H2 use 'org.h2.Driver'. MySQL driver class name is 'com.mysql.jdbc.Driver'.

database.url config
Database connection URL. By default Traccar uses H2 database.

database.user config
Database user name. Default administrator user for H2 database is 'sa'.

database.password config
Database user password. Default password for H2 admin (sa) user is empty.

database.changelog config
Path to Liquibase master changelog file.

Default value: "./schema/changelog-master.xml"

database.maxPoolSize config
Database connection pool size. Default value is defined by the HikariCP library.

database.checkConnection config
SQL query to check connection status. Default value is 'SELECT 1'. For Oracle database you can use 'SELECT 1 FROM DUAL'.

Default value: "SELECT 1"

database.saveOriginal config
Store original HEX or string data as "raw" attribute in the corresponding position.

database.throttleUnknown config
Throttle unknown device database queries when it sends repeated requests.

database.registerUnknown config
Automatically register unknown devices in the database.

database.registerUnknown.defaultCategory config
Default category for auto-registered devices.

database.registerUnknown.defaultGroupId config
The group id assigned to auto-registered devices.

database.registerUnknown.regex config
Automatically register unknown devices with regex filter.

database.saveEmpty config
Store empty messages as positions. For example, heartbeats.

users.defaultDeviceLimit config
Device limit for self registered users. Default value is -1, which indicates no limit.

Default value: -1

users.defaultExpirationDays config
Default user expiration for self registered users. Value is in days. By default no expiration is set.

ldap.url config
LDAP server URL. For more info check LDAP config.

ldap.user config
LDAP server login.

ldap.password config
LDAP server password.

ldap.force config
Force LDAP authentication.

ldap.base config
LDAP user search base.

ldap.idAttribute config
LDAP attribute used as user id. Default value is 'uid'.

Default value: "uid"

ldap.nameAttribute config
LDAP attribute used as username. Default value is 'cn'.

Default value: "cn"

ldap.mailAttribute config
LDAP attribute used as user email. Default value is 'mail'.

Default value: "mail"

ldap.searchFilter config
LDAP custom search filter. If not specified, '({idAttribute}=:login)' will be used as a filter.

ldap.adminFilter config
LDAP custom admin search filter.

ldap.adminGroup config
LDAP admin user group. Used if custom admin filter is not specified.

openid.clients config
List of OpenID Connect clients for the built-in provider. Value should be a comma-separated list of 'clientId:clientSecret' pairs.

openid.force config
Force OpenID Connect authentication. When enabled, the Traccar login page will be skipped and users are redirected to the OpenID Connect provider.

openid.clientId config
OpenID Connect Client ID. This is a unique ID assigned to each application you register with your identity provider. Required to enable SSO.

openid.clientSecret config
OpenID Connect Client Secret. This is a secret assigned to each application you register with your identity provider. Required to enable SSO.

openid.issuerUrl config
OpenID Connect Issuer (Base) URL. This is used to automatically configure the authorization, token and user info URLs if provided.

openid.authUrl config
OpenID Connect Authorization URL. This can usually be found in the documentation of your identity provider or by using the well-known configuration endpoint, e.g. https://auth.example.com/.well-known/openid-configuration Required to enable SSO if openid.issuerUrl is not set.

openid.tokenUrl config
OpenID Connect Token URL. This can be found in the same ways at openid.authUrl. Required to enable SSO if openid.issuerUrl is not set.

openid.userInfoUrl config
OpenID Connect User Info URL. This can be found in the same ways at openid.authUrl. Required to enable SSO if openid.issuerUrl is not set.

openid.groupsClaimName config
OpenID Connect group scope claim name. If this is not provided, Traccar will use name "groups" scope.

Default value: "groups"

openid.allowGroup config
OpenID Connect group to restrict access to. If this is not provided, all OpenID users will have access to Traccar. This option will only work if your OpenID provider supports the groups scope.

openid.adminGroup config
OpenID Connect group to grant admin access. If this is not provided, no groups will be granted admin access. This option will only work if your OpenID provider supports the groups scope.

status.timeout config
If no data is reported by a device for the given amount of time, status changes from online to unknown. Value is in seconds. Default timeout is 10 minutes.

Default value: 600L

status.ignoreOffline config
List of protocol names to ignore offline status. Can be useful to not trigger status change when devices are configured to disconnect after reporting a batch of data.

media.path config
Path to the media folder. Server stores audio, video and photo files in that folder. Sub-folders will be automatically created for each device by unique id.

Default value: "./media"

web.address config
Optional parameter to specify network interface for web interface to bind to. By default server will bind to all available interfaces.

web.port config
Web interface TCP port number. By default, Traccar uses port 8082. To avoid specifying port in the browser you can set it to 80 (default HTTP port).

Default value: 8082

web.maxRequestsPerSec config
Maximum API requests per second. Above this limit requests and delayed and throttled.

web.maxRequestSec config
Maximum API request duration in seconds.

Default value: 600

web.path config
Path to the web app folder.

Default value: "./web"

web.override config
Path to a folder with overrides. It can be used for branding to keep custom logos in a separate place.

Default value: "./override"

web.timeout config
WebSocket connection timeout in milliseconds. Default timeout is 5 minutes.

Default value: 300000L

web.sessionTimeout config
Authentication sessions timeout in seconds. By default no timeout.

web.console config
Enable database access console via '/console' URL. Use only for debugging. Never use in production.

web.debug config
Server debug version of the web app. Not recommended to use for performance reasons. It is intended to be used for development and debugging purposes.

web.serviceAccountToken config
A token to login as a virtual admin account. Can be used to restore access in case of issues with regular admin login. For example, if password is lost and can't be restored.

web.origin config
Cross-origin resource sharing origin header value.

web.cacheControl config
Cache control header value. By default, resources are cached for one hour.

Default value: "max-age=3600,public"

web.localizationPath config
Path to localization files.

Default value: "./templates/translations"

totpEnable server
Enable TOTP authentication on the server.

totpForce server
Server attribute that indicates that TOTP authentication is required for new users.

server.forward config
Host for raw data forwarding.

forward.type config
Position forwarding format. Available options are "url", "json" and "kafka". Default is "url".

Default value: "url"

forward.exchange config
Position forwarding AMQP exchange.

Default value: "traccar"

forward.topic config
Position forwarding Kafka topic or AQMP Routing Key.

Default value: "positions"

forward.url config device
URL to forward positions. Data is passed through URL parameters. For example, {uniqueId} for device identifier, {latitude} and {longitude} for coordinates.

forward.header config
Additional HTTP header, can be used for authorization.

forward.retry.enable config
Position forwarding retrying enable. When enabled, additional attempts are made to deliver positions. If initial delivery fails, because of an unreachable server or an HTTP response different from '2xx', the software waits for 'forward.retry.delay' milliseconds to retry delivery. On subsequent failures, this delay is duplicated. If forwarding is retried for 'forward.retry.count', retrying is canceled and the position is dropped. Positions pending to be delivered are limited to 'forward.retry.limit'. If this limit is reached, positions get discarded.

forward.retry.delay config
Position forwarding retry first delay in milliseconds. Can be set to anything greater than 0. Defaults to 100 milliseconds.

Default value: 100

forward.retry.count config
Position forwarding retry maximum retries. Can be set to anything greater than 0. Defaults to 10 retries.

Default value: 10

forward.retry.limit config
Position forwarding retry pending positions limit. Can be set to anything greater than 0. Defaults to 100 positions.

Default value: 100

event.forward.type config
Events forwarding format. Available options are "json" and "kafka". Default is "json".

Default value: "json"

event.forward.exchange config
Events forwarding AMQP exchange.

Default value: "traccar"

event.forward.topic config
Events forwarding Kafka topic or AQMP Routing Key.

Default value: "events"

event.forward.url config
Events forwarding URL.

event.forward.header config
Events forwarding headers. Example value: FirstHeader: hello SecondHeader: world

templates.root config
Root folder for all template files.

Default value: "templates"

mail.debug config
Log emails instead of sending them via SMTP. Intended for testing purposes only.

mail.smtp.systemOnly config
Restrict global SMTP configuration to system messages only (e.g. password reset).

mail.smtp.ignoreUserConfig config
Force SMTP settings from the config file and ignore user attributes.

mail.smtp.host config user
The SMTP server to connect to.

mail.smtp.port config user
The SMTP server port to connect. Defaults to 25.

Default value: 25

mail.transport.protocol config user
Email transport protocol. Default value is "smtp".

Default value: "smtp"

mail.smtp.starttls.enable config user
If true, enables the use of the STARTTLS command (if supported by the server) to switch the connection to a TLS-protected connection before issuing any login commands.

mail.smtp.starttls.required config user
If true, requires the use of the STARTTLS command. If the server doesn't support the STARTTLS command, or the command fails, the connect method will fail.

mail.smtp.ssl.enable config user
If set to true, use SSL to connect and use the SSL port by default.

mail.smtp.ssl.trust config user
If set to "*", all hosts are trusted. If set to a whitespace separated list of hosts, those hosts are trusted. Otherwise, trust depends on the certificate the server presents.

mail.smtp.ssl.protocols config user
Specifies the SSL protocols that will be enabled for SSL connections.

mail.smtp.username config user
SMTP connection username.

mail.smtp.password config user
SMTP connection password.

mail.smtp.from config user
Email address to use for SMTP MAIL command.

mail.smtp.fromName config user
The personal name for the email from address.

sms.http.url config
SMS API service full URL. Enables SMS commands and notifications.

sms.http.authorizationHeader config
SMS API authorization header name. Default value is 'Authorization'.

Default value: "Authorization"

sms.http.authorization config
SMS API authorization header value. This value takes precedence over user and password.

sms.http.user config
SMS API basic authentication user.

sms.http.password config
SMS API basic authentication password.

sms.http.template config
SMS API body template. Placeholders {phone} and {message} can be used in the template. If value starts with '{' or '[', server automatically assumes JSON format.

sms.aws.access config
AWS Access Key with SNS permission.

sms.aws.secret config
AWS Secret Access Key with SNS permission.

sms.aws.region config
AWS Region for SNS service. Make sure to use regions that are supported for messaging.

command.sender device
Command sender type for the device. This overrides standard data or text commands with an API-based commands. For example, it can be Traccar Client push commands.

command.client.serviceAccount config
Firebase service account JSON for push commands.

command.findHub.url device
Google Find Hub service URL.

command.findHub.key device
Google Find Hub service API key.

notificator.types config
Enabled notification options. Comma-separated string is expected. Example: web,mail,sms

Default value: "web,mail,command"

notificator.timeThreshold config
If the event time is too old, we should not send notifications. This parameter is the threshold value in milliseconds. Default value is 15 minutes.

Default value: 15 * 60 * 1000L

notificator.traccar.key config
Traccar notification API key.

notificator.firebase.serviceAccount config
Firebase service account JSON for push notifications.

notificator.pushover.user config
Pushover notification user name.

notificator.pushover.token config
Pushover notification user token.

notificator.telegram.key config
Telegram notification API key.

notificator.telegram.chatId config
Telegram notification chat id to post messages to.

notificator.telegram.sendLocation config
Telegram notification send location message.

notification.expiration.user config
Enable user expiration email notification.

notification.expiration.user.reminder config
User expiration reminder. Value in milliseconds.

notification.expiration.device config
Enable device expiration email notification.

notification.expiration.device.reminder config
Device expiration reminder. Value in milliseconds.

notification.block.users config
Block notifications for specific users. The value should be a comma-separated list of internal user ids.

report.periodLimit config
Maximum time period for reports in seconds. Can be useful to prevent users to request unreasonably long reports. By default, there is no limit.

report.fastThreshold config
Time threshold for fast reports. Fast reports are more efficient, but less accurate and missing some information. The value is in seconds. One day by default.

Default value: 86400L

report.trip.minimalTripDistance config device
Trips less than minimal duration and minimal distance are ignored. 300 seconds and 500 meters are default.

Default value: 500L

report.trip.minimalTripDuration config device
Trips less than minimal duration and minimal distance are ignored. 300 seconds and 500 meters are default.

Default value: 300L

report.trip.minimalParkingDuration config device
Parking less than minimal duration does not cut trip. Default 300 seconds.

Default value: 300L

report.trip.minimalNoDataDuration config device
Gaps of more than specified time are counted as stops. Default value is one hour.

Default value: 3600L

report.trip.useIgnition config device
Flag to enable ignition use for trips calculation.

Default value: false

report.ignoreOdometer config device
Ignore odometer value reported by the device and use server-calculated total distance instead. This is useful if device reports invalid or zero odometer values.

Default value: false

filter.enable config
Boolean flag to enable or disable position filtering.

Default value: true

filter.invalid config
Filter invalid (valid field is set to false) positions.

filter.zero config
Filter zero coordinates. Zero latitude and longitude are theoretically valid values, but it practice it usually indicates invalid GPS data.

filter.duplicate config
Filter duplicate records (duplicates are detected by time value).

filter.outdated config
Filter messages that do not have GPS location. If they are not filtered, they will include the last known location.

filter.future config
Filter records with fix time in the future. The value is specified in seconds. Records that have fix time more than the specified number of seconds later than current server time would be filtered out.

Default value: 86400L

filter.past config
Filter records with fix time in the past. The value is specified in seconds. Records that have fix time more than the specified number of seconds before current server time would be filtered out.

filter.accuracy config
Filter positions with accuracy less than specified value in meters.

filter.approximate config
Filter cell and wifi locations that are coming from geolocation provider.

filter.static config
Filter positions with exactly zero speed values.

filter.distance config
Filter records by distance. The values is specified in meters. If the new position is less far than this value from the last one it gets filtered out.

filter.maxSpeed config
Filter records by Maximum Speed value in knots. Can be used to filter jumps to far locations even if Position appears valid or if Position `speed` field reported by the device is also within limits. Calculates speed from the distance to the previous position and the elapsed time. Tip: Shouldn't be too low. Start testing with values at about 25000.

filter.minPeriod config
Filter position if time from previous position is less than specified value in seconds.

filter.dailyLimit config
Throttle positions if the daily limit is exceeded for the device.

filter.dailyLimitInterval config
Throttling interval if the limit exceeded. The value is in seconds.

filter.relative config
If false, the server expects all locations to come sequentially (for each device). Filter checks for duplicates, distance, speed, or time period only against the location that was last received by server. If true, the server expects locations to come at random order (since tracking device might go offline). Filter checks for duplicates, distance, speed, or time period against the preceding Position's. Important: setting to true can cause potential performance issues.

filter.skipLimit config
Time limit for the filtering in seconds. If the time difference between the last position was received by server and a new position is received by server is more than this limit, the new position will not be filtered out.

filter.skipAttributes.enable config
Enable attributes skipping. Attribute skipping can be enabled in the config or device attributes. If position contains any attribute mentioned in "filter.skipAttributes" config key, position is not filtered out.

filter.skipAttributes config device
Attribute skipping can be enabled in the config or device attributes. If position contains any attribute mentioned in "filter.skipAttributes" config key, position is not filtered out.

Default value: ""

time.override config
Override device time. Possible values are 'deviceTime' and 'serverTime'

protocols.enable config
List of protocols to enable. If not specified, Traccar enabled all protocols that have port numbers listed. The value is a comma-separated list of protocol names. Example value: teltonika,osmand

time.protocols config
List of protocols for overriding time. If not specified override is applied globally. List consist of protocol names that can be separated by comma or single space character.

coordinates.filter config
Replaces coordinates with last known if change is less than a 'coordinates.minError' meters or more than a 'coordinates.maxError' meters. Helps to avoid coordinates jumps during parking period or jumps to zero coordinates.

coordinates.minError config
Distance in meters. Distances below this value gets handled like explained in 'coordinates.filter'.

coordinates.maxError config
Distance in meters. Distances above this value gets handled like explained in 'coordinates.filter'.

processing.remoteAddress.enable config
Enable to save device IP addresses information. Disabled by default.

processing.useLinkedDriver config
Use linked driver id for positions if a device does not send driver id.

processing.copyAttributes.enable config
Enable copying of missing attributes from last position to the current one. Might be useful if device doesn't send some values in every message.

processing.copyAttributes config device
List of attributes to copy. Attributes should be separated by a comma without any spacing. For example: alarm,ignition

processing.computedAttributes.deviceAttributes config
Include device attributes in the computed attribute context.

processing.computedAttributes.lastAttributes config
Include last position attributes in the computed attribute context.

processing.computedAttributes.localVariables config
Enable local variables declaration.

processing.computedAttributes.loops config
Enable loops processing.

processing.computedAttributes.newInstanceCreation config
Enable new instances creation. When disabled, parsing a script/expression using 'new(...)' will throw a parsing exception;

geocoder.enable config
Boolean flag to enable or disable reverse geocoder.

Default value: true

geocoder.type config
Reverse geocoder type. Check reverse geocoding documentation for more info.

Default value: "locationiq"

geocoder.url config
Geocoder server URL. Applicable only to Nominatim and Gisgraphy providers.

geocoder.key config
Provider API key. Most providers require API keys.

Default value: "pk.689d849289c8c63708068b2ff1f63b2d"

geocoder.language config
Language parameter for providers that support localization (e.g. Google and Nominatim).

geocoder.format config
Address format string. Default value is %h %r, %t, %s, %c. See AddressFormat for more info.

geocoder.cacheSize config
Cache size for geocoding results.

geocoder.ignorePositions config
Disable automatic reverse geocoding requests for all positions.

Default value: true

geocoder.reuseDistance config
Optional parameter to specify minimum distance for new reverse geocoding request. If distance is less than specified value (in meters), then Traccar will reuse last known address.

geocoder.onRequest config
Perform geocoding when preparing reports and sending notifications.

Default value: true

geolocation.enable config
Boolean flag to enable LBS location resolution. Some devices send cell towers information and WiFi point when GPS location is not available. Traccar can determine coordinates based on that information using third party services. Default value is false.

geolocation.type config
Provider to use for LBS location. Available options: google, unwired and opencellid. By default, google is used. You have to supply a key that you get from corresponding provider. For more information see LBS geolocation documentation.

geolocation.url config
Geolocation provider API URL address. Not required for most providers.

geolocation.key config
Provider API key. OpenCellID service requires API key.

geolocation.processInvalidPositions config
Boolean flag to apply geolocation to invalid positions.

geolocation.reuse config
Reuse last geolocation result if network details have not changed.

geolocation.requireWifi config
Process geolocation only when Wi-Fi information is available. This makes the result more accurate.

geolocation.mcc config
Default MCC value to use if device doesn't report MCC.

geolocation.mnc config
Default MNC value to use if device doesn't report MNC.

speedLimit.enable config
Boolean flag to enable speed limit API to get speed limit values depending on location. Default value is false.

speedLimit.type config
Provider to use for speed limit. Available options: overpass. By default overpass is used.

speedLimit.url config
Speed limit provider API URL address.

speedLimit.accuracy config
Search radius for speed limit. Value is in meters. Default value is 100.

Default value: 100

location.latitudeHemisphere config
Override latitude sign / hemisphere. Useful in cases where value is incorrect because of device bug. Value can be N for North or S for South.

location.longitudeHemisphere config
Override longitude sign / hemisphere. Useful in cases where value is incorrect because of device bug. Value can be E for East or W for West.

web.requestLog.path config
Jetty Request Log Path. The path must include the string "yyyy_mm_dd", which is replaced with the actual date when creating and rolling over the file. Example: ./logs/jetty-yyyy_mm_dd.request.log

web.requestLog.retainDays config
Set the number of days before rotated request log files are deleted.

web.disableHealthCheck config
Disable systemd health checks.

web.healthCheck.dropThreshold config
If this parameter is set, Traccar will monitor drops in the number of stored messages. If it drops more than the threshold, it will mark service as failing for systemd. Threshold is a value from 0.0 to 1.0. For example, value 0.7 means that the number of messages in the last period is only 70% of what it was in the previous.

web.sameSiteCookie config
Sets SameSite cookie attribute value. Supported options: Lax, Strict, None.

web.persistSession config
Enables persisting Jetty session to the database

web.url config
Public URL for the web app. Used for notification, report link and OpenID Connect. If not provided, Traccar will attempt to get a URL from the server IP address, but it might be a local address.

web.showUnknownDevices config
Show logs from unknown devices.

web.shareDevice.commands config
Enable commands for a shared device.

web.shareDevice.reports config
Enable reports for a shared device.

web.mcp.enable config
Enable MCP service.

logger.console config
Output logging to the standard terminal output instead of a log file.

logger.queries config
Log executed SQL queries.

logger.file config
Log file name. For rotating logs, a date is added at the end of the file name for non-current logs.

Default value: "./logs/tracker-server.log"

logger.level config
Logging level. Default value is 'info'. Available options: off, severe, warning, info, config, fine, finer, finest, all.

Default value: "info"

logger.fullStackTraces config
Print full exception traces. Useful for debugging. By default shortened traces are logged.

logger.rotate config
Create a new log file daily. Helps with log management. For example, downloading and cleaning logs. Enabled by default.

Default value: true

logger.decodeTextData config
If all bytes are printable characters, log network data as text instead of HEX.

Default value: true

logger.rotate.interval config
Log file rotation interval, the default rotation interval is once a day. This option is ignored if 'logger.rotate' = false Available options: day, hour

Default value: "day"

logger.attributes config
A list of position attributes to log.

Default value: "time,position,speed,course,accuracy,result"

broadcast.type config
Broadcast method. Available options are "multicast" and "redis". By default, (if the value is not specified or does not match available options) server disables broadcast.

broadcast.interface config
Multicast interface. It can be either an IP address or an interface name.

broadcast.address config
Multicast address or Redis URL for broadcasting synchronization events.

broadcast.port config
Multicast port for broadcasting synchronization events.

broadcast.secondary config
Flag to mark secondary servers. Some tasks, like scheduled reports, will be executed on the main server only.
