LBS Geolocation
Traccar can use Wi-Fi and cell tower information to determine location when GPS is not available. Sometimes this feature is referred to as LBS or geolocation.

Currently Traccar supports following geolocation providers:

Google
Proprietary API provided by Google. It has the best accuracy among all available options.

Configuration parameters:

<entry key='geolocation.enable'>true</entry>
<entry key='geolocation.type'>google</entry>
<entry key='geolocation.key'>YOUR API KEY</entry>
Unwired
Geolocation API from Unwired Labs. It's the same company providing LocationIQ service.

Configuration parameters:

<entry key='geolocation.enable'>true</entry>
<entry key='geolocation.type'>unwired</entry>
<entry key='geolocation.url'>https://us1.unwiredlabs.com/v2/process</entry>
<entry key='geolocation.key'>YOUR API KEY</entry>
OpenCelliD
Free and most open provider, but it can only use single cell tower for location and doesn't support Wi-Fi at all. This results in a very low location quality.

Configuration parameters:

<entry key='geolocation.enable'>true</entry>
<entry key='geolocation.type'>opencellid</entry>
<entry key='geolocation.key'>YOUR API KEY</entry>
