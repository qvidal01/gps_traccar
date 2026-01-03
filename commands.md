Commands
Most of GPS devices support commands. They can be used for device configuration, changing state or requesting information. Commands can be sent via the same network connection used to upload data from the device to the server or via SMS. Many devices support both options.

Traccar command support varries by protocol, but all of the protocols should support at least the custom command. Custom command must be in HEX format for most binary protocols and in text for text-based protocols.

SMS needs to be enabled on the server to send command via SMS, and device have to have the phone field set correctly. See SMS API configuration for more information on how to enabled SMS.

Command results are supported for a limited number of protocols. If it is received and decoded, a command result event will be generated.

In case a command is sent via the network connection and the device in not online, it will be queued. All commands from the device queue will be sent as soon as the device becomes online. It is not recommended to put a lot of commands in queue because some devices ignore next command until they handle previous.

By default all regular users can send commands. It is possible to restrict users from sending arbitrary commands using limit commands setting in the account permissions. Such user can only send pre-defined saved commands linked to their account.

Saved Commands
Often users need to send the same set of commands frequently. Saved commands is a way to store a command with parameters to be used later. It is the most useful for the custom command.

User can create a saved command with desired parameters from the corresponding setting menu:


Then link it to a device or a group of devices:


To send the command, select it from the list on the send command screen:

