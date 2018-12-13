X-mas lights FastLED + Lolin D1 Mini (ESP8266) Web Server
=========

Detta är en mjukvara för att med en Lolin D1 Mini och en Levelshift Shield, kunna fjärrstyra en LED-slinga via ett webbinterface. Den flashas via Arduino IDE till en ESP8266 (i vårt fall Lolin D1 Mini).

Hårdvara
--------

Vi använder mjukvaran till Lolin D1 Mini och WS2801-slingor.
Eftersom WS2801 kommunicerar på 5V använder vi en egentillverkad Levelshift Shield som omvandlar Lolins 3.3V-signal till 5V.
Förslag på delar:

* [Lolin D1 Mini](https://www.m.nu/esp8266/d1-mini)
* [D1 Mini Levelshift Shield](https://www.m.nu/esp8266-shields/levelshift-shield-for-wemos-d1-mini)
* [Enfärgad WS2801-slinga](https://www.m.nu/dotstar-ws2801/digital-rgb-led-slinga-ws2801-enfargad-kabel-svart-vit-1)

Spänningsmatning (**5V**) kan i detta fall anslutas direkt till slingans matningskablar som då också förser Lolinen med ström.

Observera att FastLED är kompatibelt med de flesta adresserbara LED-typer, så genom att ändra koden kan man enkelt använda andra typer (t.ex. NeoPixel, WS2811/WS2812, APA102 etc).

Funktioner
--------
* Saknas WiFi-login startar ESP:n som accesspunkt och man kan då ansluta till den och ställa in WiFi-nätverk på URL: http://192.168.4.1
* Slå av och på ljusslingan
* Justera ljusstyrka
* Ändra vilket ljusläge som visas
* Justera färg

Eventuella framtida funktioner
---------------------
* Justera hastighet
* Fler ljuslägen

Webb-applikation
--------

![Webb-app](webapp.png)

Webb-applikationen lagras på det inbyggna flashminnet på ESP-chippet (SPIFFS). Denna laddas upp separat från sketchen, vilket gås igenom nedan.

Det hela är en enkel webbsida som använder [jQuery](https://jquery.com) och [Bootstrap](http://getbootstrap.com).
Den har knappar för av/på, en slider för att ställa ljusstyrka, och dropdown-menyer för att välja färgmönster/blinkmönster. En färgväljare finns också (baserad på [jQuery MiniColors](http://labs.abeautifulsite.net/jquery-minicolors)). Alla ändringar skickas automatiskt till styrenheten, men ljusstyrkan och färgvalet skickas med fördröjning för att inte överbelasta ESP:n med anrop.

Installation
-----------
För att lägga in mjukvaran använder du Arduino IDE:n som kan [hämtas här](https://www.arduino.cc/en/main/software). Du behöver lägga till "ESP8266 Core" för att jobba mot Lolin och andra ESP-baserade chipp, detta görs under __File__ > __Preferences__, kopiera in URL:en "http://arduino.esp8266.com/stable/package_esp8266com_index.json" i __Additional Boards Manager URLs__-fältet. Klicka på OK. Klicka därefter på __Tools__ > __Boards: ...__ > __Boards Manager__. Hitta och klicka på __ESP8266__ (att söka kan förenkla detta). Klicka på __Install__. Efter installationen, klicka på __Close__ och därefter väljer du din ESP8266-variant under __Tools > Board: ...__ (i vårt fall WeMos D1 Mini).

Appen behöver följande bibliotek, som antingen kan laddas ner manuellt från Github och läggas i Arduino IDE:ns __libraries__-mapp, eller installeras enligt [instruktion här (engelska)](https://www.arduino.cc/en/Guide/Libraries) genom att använda __Arduino library manager__.

* [FastLED](https://github.com/FastLED/FastLED)
* [Arduino WebSockets](https://github.com/Links2004/arduinoWebSockets)
* [WiFi Manager](https://github.com/tzapu/WiFiManager)  (OBS! version 0.12.0 måste användas för att slippa kompileringsfel)

Ladda därefter ned koden härifrån Github, med den gröna __Clone or Download__-knappen ovan. Klicka på __Download ZIP__, och packa upp innehållet i din Arduino-sketchmapp.
Gör eventuella modifikationer (t.ex. antal LEDs och **WiFi-inlogg**) och ladda därefter upp koden med Upload-knappen i IDE:n. Kom ihåg att välja rätt ESP-typ och seriellport under __Tools__.

Filerna som visar webbapplikationen behöver laddas upp separat till chippets SPIFFS. För att göra det behöver du installera en plugin till Arduino IDE:n, [Arduino ESP8266FS tool](https://github.com/esp8266/Arduino/blob/master/doc/filesystem.rst#uploading-files-to-file-system).
Ladda ner filen. Den ska sen packas upp i Arduino/tools-mappen enligt instruktionerna. När du lagt in den där och startat om IDE:n kan du öppna sketchen och klicka __Tools > ESP8266 Sketch Data Upload__, vilket laddar upp filerna till minnet. Observera att detta kan gå ganska långsamt, ibland tar det flera minuter.

Komprimering
------------
Det är möjligt att komprimera filerna med gzip innan uppladdning för att spara plats, se huvudrepots instruktioner för direktiv då vi inte sett någon anledning att göra det.

REST-tjänst
-----------------

Mjukvaran tillhandahåller, förutom webbsidan, en enkel ["RESTful" webbtjänst](https://en.wikipedia.org/wiki/Representational_state_transfer) med hjälp av __ESP8266WebServer__-biblioteket. Befintliga värden hämtas med HTTP GETs, och nya värden sätts med POSTs och parametrar i anropet.  Enheten kan ansluta till en befintlig router/accesspunkt eller som fristående accesspunkt.

IR-styrning
-------------
Huvudrepot har IR-styrning, men detta såg vi som onödigt och valde att plocka bort det ur koden.
