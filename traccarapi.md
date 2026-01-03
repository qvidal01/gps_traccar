Traccar API
Traccar provides an API to access all service functionality. Detailed documentation can be found in the API Reference or the OpenAPI spec file.

There are several user authorization options:

Using session cookies (see session endpoint)
Standard HTTP authorization header
Bearer token authentication
Official Traccar API documentation:

API Reference
Access token
As an alternative to the email and password login, there is an option to use an account token for authorization. A token can be generated from the web app or via an API call.

To create a session use token query parameter in session get request:

/api/session?token=USER_TOKEN
WebSocket API
In addition to the REST API, we provide an access to a WebSocket endpoint for live updates. Endpoint for the WebSocket connection:

/api/socket
Session cookie is the only authorization option for the WebSocket connection.

Each message in the WebSocket stream uses the same universal JSON format:

{
  "devices": [...],
  "positions: [...],
  "events": [...]
}
Each array contains standard JSON objects:

Device
Position
Event
If a message does not contain any objects of a certain type, the key would not be included in the JSON structure. In most cases a message contain a single type of objects.
