Forwarding Overview
Traccar can forward information to external systems for further processing or integration. The platform supports three complementary forwarding flows: processed positions, generated events, and raw protocol traffic. This document summarizes what each option does and how to configure it.

Position Forwarding
Position forwarding pushes device position updates after they have been decoded and enriched by Traccar.

forward.url (required) - Destination endpoint. Can be set globally or per device.
forward.type - Delivery format. Options: url (HTTP query parameters), json (HTTP JSON payload), amqp, kafka,mqtt, redis, wialon. Defaults to url.
forward.header - Optional extra HTTP header for authentication or metadata.
forward.exchange - AMQP exchange name (used by AMQP forwarding).
forward.topic - Kafka topic, AMQP routing key, or MQTT topic depending on the transport.
forward.retry.enable - Enables retries when delivery fails.
forward.retry.delay - Initial retry delay in milliseconds. Default 100.
forward.retry.count - Maximum number of retry attempts. Default 10.
forward.retry.limit - Maximum queued positions awaiting retry. Default 100.
Event Forwarding
Event forwarding delivers Traccar events (e.g., alarms, device status changes) to downstream systems in near real time.

event.forward.url (required) - Destination endpoint for event messages.
event.forward.type - Delivery format. Options: json, amqp, kafka, mqtt. Defaults to json.
event.forward.header - Optional custom headers for HTTP-based forwarding.
event.forward.exchange - AMQP exchange name.
event.forward.topic - Kafka topic, AMQP routing key, or MQTT topic.
Raw Data Forwarding
The server.forward setting mirrors the raw TCP or UDP packets that devices send to Traccar. Use this when anotherservice needs the original protocol payloads alongside Traccar's processing.

server.forward (required) - Hostname or IP address of the target listener.
Protocol-specific port keys (protocol.port) - Each protocol forwards to the same port number configured for that protocol on the Traccar server.
With the above configuration, raw GT06 data received on gt06.port is duplicated to 192.0.2.10:5023 using the sametransport (TCP or UDP) as the original connection.

