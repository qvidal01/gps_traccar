Traccar Architecture
====================

Overview
--------
Traccar is a modular GPS tracking platform with three main components:
- Protocol servers built on Netty for device connectivity and command delivery.
- An embedded Jetty HTTP server that exposes REST/WebSocket APIs and serves the web UI.
- A React single-page application for administration and monitoring.

Protocol and Netty pipeline
---------------------------
Each device connection is handled by a Netty channel with a protocol-specific pipeline:
- Frame decoder (TCP) – splits incoming byte streams into complete protocol frames.
- String encoder/decoder (text protocols) – converts between bytes and text.
- Protocol encoder/decoder – maps between protocol-specific payloads and Traccar’s internal position/command models.
- Utility handlers – distance calculations, reverse geocoding, and other enrichments.
- Event handlers – derive events (geofence, alarms, movement, etc.) from position data.
- Data handler – persists decoded data into the configured SQL database.
- Main handler – manages connection lifecycle, logging, and error handling.

Protocol classes assemble these handlers and declare supported device commands. Outbound commands traverse the pipeline in reverse order before being sent to the device.

Events and notifications
------------------------
Events originate either from devices or from server-side handlers that inspect position data. Delivery channels are pluggable and configurable per installation:
- Email via SMTP
- SMS via HTTP API provider
- Web notifications over WebSocket
- Push notifications through Firebase

Notification dispatchers (“Notificators”) can be toggled in the server configuration.

Data and persistence
--------------------
- SQL databases: MySQL, PostgreSQL/TimescaleDB, Microsoft SQL Server, Oracle, and others via JDBC.
- Schema management: Liquibase change logs create and migrate schema at runtime.
- Caching: recent device state is cached in memory for fast lookups; use API calls instead of direct SQL writes to avoid stale caches.

Web/API surface
---------------
- REST API: CRUD endpoints for core models (devices, users, geofences, notifications, etc.) using standard HTTP verbs.
- WebSocket: live channel for status, location updates, and events.
- Auth: shared authentication/authorization for REST and WebSocket.
- Server: Jetty hosts both the API and the static assets for the web UI.

Web application
---------------
- Built with React and Material UI; uses Redux for state and React Router for navigation.
- Served as static files by the embedded Jetty instance.
- Relies on the API/WebSocket described above for data and live updates.
