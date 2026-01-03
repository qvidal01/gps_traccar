Protocol Identification
As the first step, try finding your device in the list of supported devices. The last column contains default port number for the device. If you have a device made in China, make sure to also check the information about clones.

If a given port does not work for your device or you cannot find your device in the list of supported devices, try collecting some samples using the following steps:

Configure your device to send data to the port 5001
Wait for the device to send a few messages to the server
Find HEX message from the device in the log file
Once you have some samples and you are sure those samples are from your device, you can try matching them with common patterns below or compare to test cases we have for various protocols:

Test cases for protocols
If protocol is text-based, you have to convert HEX to text first.

Common protocol patterns
If your device is using GT06 protocol (correct port 5023), HEX samples will start with 7878 or 7979, like this:

78780D01086471700328358100093F040D0A
If your device is using Huabao protocol (correct port 5015), HEX samples will start and end with 7e. Note that there are some other protocols that use 7e, so it's not a guaranteed match. Example:

7E01000021013345678906000F002C012F373031313142534A2D4D3742203030303030303001D4C1423838383838B47E
If your device is using GPS103 protocol (correct port 5001), decoded message text would start with imei: and end with a semi-colon (;). Example:

imei:864035050002451,tracker,201223064947,,F,064947,A,1935.70640,N,09859.94436,W,0.025,;
If your device is using H02 protocol (correct port 5013), decoded message text would start with *HQ, and end with a number sign (#). Note that some H02-based devices send a mix of text and binary messages. Text message example:

*HQ,4210209006,V1,201844,A,2608.9437,N,08016.2521,W,000.80,000,150317,FFFFF9FF,310,260,0,0,6#
If your device is using Watch protocol (correct port 5093), decoded message text would start with opening square bracket followed by 2 upper-case characters, followed by device id surrounded by star signs, like [3G*8308373902*, and end with a closing square bracket. Example:

[SG*9051004074*0058*AL,120117,145602,V,40.058413,N,76.336618,W,11.519,188,99,00,01,80,0,50,00000000,0,1,0,0,,10]
