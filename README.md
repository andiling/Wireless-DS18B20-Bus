# WirelessDS18B20

This project use an ESP8266 and some DS18B20 sensors on a 1-Wire bus to provide temperatures in JSON format (Jeedom compatible).
Since version 3.2.1, temperatures can be sent regularly to an MQTT server too.

The global idea of this project is that any system (like Jeedom) that's able to do an HTTP GET request and interpret JSON will get list of DS18B20 ROMcode available on buses or current temperature of a DS18B20 sensor.
MQTT compatible device will also be able to receive pushed temperature.

![getList](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/getL.jpg) ![getTemp](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/getT.jpg)

## Build your 1-Wire bus

In order to get a wide 1-Wire bus (one that cover a house), Dallas provides some recommendations into their application note AN148.

This project use the Improved Interface bellow that is able to support a 200m bus:

![Dallas AN148 ImprovedCPUBusInterface schema](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/AN148-ImprovedCPUBusInterface.jpg)

This one requires a pin for reading the bus state and another one as output to drive the bus low : I named it the "Dual Pin OneWire"

**Warning : If you build a large 1 Wire bus inside your house, keep in mind that some high voltage may appear on this one by induction. You need to have a good knowledge about electricity and associated risks!!!**

## Build your WirelessDS18B20

All files are inside schematic subfolder and has been designed with KiCad

### Schematic

![WirelessDS18B20 schematic](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/schematic.jpg)

### PCB

![WirelessDS18B20 PCB](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/pcb.jpg)

### Code/Compile

Source code can be compiled for :

- ESP-01 : Pin usage is fixed like in schematic.
- other ESP8266 models : 1-Wire buses pins can be configured through the configuration webpage

## Run

### First Boot

During First Boot, the ESP boot in Access Point Mode to allow you configuration

- Network SSID : `WirelessDSXXXX`
- Password : `PasswordDS`
- ESP IP : `192.168.4.1`

Connect to this network and then configure it.

### Configuration

WirelessDS18B20 offers you some webpages in order to configure it :

- `Status` return you the current status of the module :

![status screenshot](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/status.png)

- `Config` allows you to change configuration (Wifi and 1-Wire buses (on non-ESP01)) :

![config screenshot](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/config.png)

- **ssid & password** : IDs of your Wifi Network
- **hostname** : name of ESP on the network
- **IP,GW,NetMask,DNS1&2** : Fixed IP configuration

![config2 screenshot](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/config2.png)

- **number of OneWire buses** : number of OneWire buses...
- **buses pin numbers** : pins for each oneWire buses (2 pins per OW bus)

![config3 screenshot](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/config3.png)

- **HA Type** : None (HTTP GET only) or MQTT
- **SSL/TLS** : check if your MQTT server enforce SSL/TLS
- **Hostname** : IP or DNS name of your MQTT server
- **Upload Period** : Period in seconds betwwen each MQTT send
- **MQTT Type** : describe the structure of the topic end
- **Port** : MQTT Port
- **Username/Password** : MQTT Username/Password (both are optionnal)
- **Base Topic** : Prefix of the topic

- `Firmware` allows you to flash a new firmware version :

![firmware screenshot](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/firmware.png)

- `Discover` allows you to find all DomoChip devices on your network :

![discover screenshot](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/discover.png)

### Rescue Mode

If you lost access to your WirelessDS18B20, you can `reset it` (reset button or power off) and during the 5 first seconds, `press the "Rescue Mode" button` to start it with default config (like during First Boot).

## Use it

### Basics

Usage (answers are in JSON format):

- `http://IP/getL?bus0` will return list of DS18B20 ROMCodes available on the 1-Wire bus number 0
- `http://IP/getT?bus0=0A1B2C3D4E5F6071` will return simple JSON with temperature from the sensor

### With Jeedom (HTTP GET Method)

> Memento : Jeedom is an innovative home automation system that can be found at <http://jeedom.com>

For this configuration you need the *Script* plugin installed from the market :

![Script Icon](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptIcon.png)

Go to script plugin then add a new equipment:

![Script Add](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptAdd.png)

Name it :

![Script Name](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptName.png)

Set refresh time (every minutes in my case) :

![Script Refresh](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptRefresh.png)

Then into command tab, add a script command :

![Script Command](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptAddCmd.png)

Then set it up with :

- command name : Temperature
- script type : JSON
- request : Temperature (JSON tag name)
- URL : `http://**IP**/getT?bus**0**=**0A1B2C3D4E5F6071**`
- Unit : °C

![Script Command Config](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptCmdConfig.png)

Now you're done and should get something like this :

![Script Command Result](https://raw.github.com/Domochip/Wireless-DS18B20-Bus/master/img/JeedomScriptResult.png)