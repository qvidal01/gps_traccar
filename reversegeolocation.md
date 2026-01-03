Reverse Geocoding
Reverse geocoding is the process of converting location into a street addresses. Traccar supports several reverse geocoding providers, including some open source options.

LocationIQ
LocationIQ combines multiple datasets (OpenAddress, GNAF, OSM, Geonames, etc) to provide affordable geolocation APIs worldwide. You need to signup for an account at LocationIQ.com to get your API key.

There is a generous free monthly quota and several paid plans available.

Configuration parameters for LocationIQ:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>locationiq</entry>
<entry key='geocoder.key'>YOUR API KEY</entry>
Note: if Europe is geographically closer to you, change the url to https://eu1.locationiq.com/v1/reverse.php.

Google
Google reverse geocoding is a part of Geocoding API. It provides one of the best data quality, but it's also one of the highest prices among all available providers.

Google provides a monthly credit, so the API can be used for free up to some limit. After that you get charged a fee per 1000 requests.

Configuration parameters:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>google</entry>
<entry key='geocoder.key'>YOUR API KEY</entry>
Nominatim
Nominatim is an open source software for geocoding services. Several companies provide hosted instances of Nominatim that you can query via an API. You can host your own Nominatim server as well.

Please DO NOT use OSM hosted version of Nominatim. It should only be used for low volume testing purposes.

Configuration parameters for Nominatim server example:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>nominatim</entry>
<entry key='geocoder.url'>https://nominatim.openstreetmap.org/reverse</entry>
Gisgraphy
Gisgraphy is a free and open source framework that offers the ability to do geolocalisation and geocoding via Java APIs or REST webservices. There are number of free and paid cloud providers or you can self host a Gisgraphy server.

Configuration parameters example:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>gisgraphy</entry>
<entry key='geocoder.url'>http://services.gisgraphy.com/reversegeocoding/search</entry>
Geocode Farm
Geocode Farm is another geocoding provider. They have a small free daily quota and there several paid plans available.

Configuration parameters:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>geocodefarm</entry>
<entry key='geocoder.key'>YOUR API KEY</entry>
OpenCage Geocoder
OpenCage Geocoder combines multiple geocoding systems in the background. Each is optimized for different parts of the world and types of requests. You need to have an API key to use this type of geocoder.

Configuration parameters:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>opencage</entry>
<entry key='geocoder.url'>http://api.opencagedata.com/geocode/v1</entry>
<entry key='geocoder.key'>YOUR API KEY</entry>
Base Adresse Nationale
Base Adresse Nationale is a free service limited to French addresses and 10 requests per IP per second.

Configuration parameters:

<entry key='geocoder.enable'>true</entry>
<entry key='geocoder.type'>ban</entry>
Other providers
Other reverse geocoding providers supported by Traccar:

MapQuest
Bing Maps
Factual
Geocode.xyz
Here
MapMyIndia
TomTom
PositionStack
Additional parameters
By default reverse geocoding is only done when user requests reports or explicitly clicks "show address" button in the app. You can disable this behavior using the following parameters:

<entry key='geocoder.onRequest'>false</entry>
<entry key='geocoder.ignorePositions'>false</entry>
You can also save on the reverse geocoding API cost by avoiding requests if location hasn't changed significantly from the last one. Distance threshold is set in meters:

<entry key='geocoder.reuseDistance'>10</entry>
