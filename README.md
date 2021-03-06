# IoTain
College Minor project on Implementation of IoT using Blockchain

![Alt text](screenshots/mesh.gif "Iot Chain") 

IoTain is developed as a lite operating system using the blockchain concept and implementing PBFT, SPV and CPS technology, allowing data to be layered and stored in a decentralised manner and providing protection with the combined strength of the millions of IoT nodes within the network.

The technology will result in an economy of sharing for Internet of Things (IoT) devices in a decentralized and energy efficient environment while producing a wealth of valuable user data which can be used as fuel for the development of AI with users rewarded for providing this fuel.

![Alt text](screenshots/iot_market.png "Iot Market") 

Under this network, the interconnection of IoT devices of a home, a business or even a city can truly flourish without worry of transfer congestion, malicious attacks or losing privacy.


## Module 1: Resources 

### Capacitive Touch : IoT Potato Controlled Drum Player  ( 24/9/18 )
#### Demo Video : CLICK IMAGE TO OPEN 
[![IoT Drum Player : Potato Version](https://img.youtube.com/vi/ghmBqej5D24/0.jpg)](https://www.youtube.com/watch?v=ghmBqej5D24 "Video : IoT Drum Player : Potato Version")

## Module 2: Resource Discovery 

![Alt text](screenshots/resource_discovery.png "Resource Discovery ") 

### Internetwork Communication (CORE)  ( 02/10/18 )
To make HTTP calls from once device routed through AP and that Routed through STA mode when device is working in AP+STA mode . This will be used for Chaining Up multiple devices , request forwarding and Callbacking [See Test 2]



### Implement Intercom functions (CORE)
Write RPC listeners to listen to requests , check whether resource in avalable , if available  pass the request to resource access controller else pass the request to other devices .

Implement
```code
on_request()
on_callback()
on_resource()

```
### Resource Registry
Make a table of resources availble on a device 

## Module 3: Network Structure Creation

### WiFi Status LED behaviour (17/10/18)
LED at pin 5 in blinking state when WiFi is connecting or disconnected and Solid ON when wifi connected

### Connect to Backbone network when available
All devices connect to backbone network when in centralized mode

### Interconnect 
When backbone network goes down or when in chained mode , trigger a scan and connect to the IoT wifi SSID with largest signal 

 

## Overview of Project
![Alt text](screenshots/plan0.jpg "Main Screen")
![Alt text](screenshots/plan1.jpg "Main Screen") 

## Tests

### Test 1 : To check if a device is cabale of autoconnecting in sta mode when the AP is available   ( 01/10/18 )

#### Test Case 0 : 

After setting in config and device connects to AP , AP is restarted and device in monirotred if it rescans the AP and connects to it 
PASS

### Test 2 : To check if a device is cabale of requesting to servers on diffrent networks   ( 02/10/18 )
![Alt text](screenshots/test_0_dhcp_distribution.png "Internetwork Communivation") 

(Syntax = Stattion Name ----:> Access Point Name)
```code
iot_1 -----:> iot_0 ----:> wifi <:------ PC 
```
#### Test Case 0 : 

iot_1 makes an http call to iot_0  at a fixed IP and check if http call works  (PASS) 


#### Test Case 1 : 

iot_0 makes an http call to iot_1 (i.e. device in its AP network) at its guessed IP address (later IP table registration will be used ) and see if it gets resutls , this will be used in callbacking (PASS)


#### Test Case 2 : 

iot_0 makes an http call to PC (i.e. device in its STA Network) (PASS) 

#### Test Case 3 : 

```code
iot_1 <:-----:> iot_0  
```
Both devices are tightly coupled to each other i.e. connected to eachohers AP , but the ip addressing is in diffrerent ranges 

for iot_0 
```javascript

Cfg.set({wifi:{ap:{
  ssid:"iot_0",pass:"password",enable:true,ip:"192.168.4.1"
  ,gw:"192.168.4.1",dhcp_start:"192.168.4.2",dhcp_end:"192.168.4.49"}}});

```

for iot_1 
```javascript

    Cfg.set({wifi:{ap:{
ssid:"iot_1",pass:"password",enable:true,ip:"192.168.4.50"
,gw:"192.168.4.50",dhcp_start:"192.168.4.51",dhcp_end:"192.168.4.100"}}});

```

iot_0 makes an http call to iot_1 , iot_1 makes an http call to iot_0  (PASS) 

### Conclusions

In Image

````code

Top Terminal Shows Log on PC Endpoint
Left Terminal Shows Log on iot_0
Right Terminal Shows Log on iot_1

````


Idea is that when a device connected iot_0's AP makes a request to access PC (which is connected to same AP as iot_0) then iot_0 will recieve this request and make another request to PC , the result from the PC is required to be forwarded back to iot_1 which made the initial request thus facilitating callbacks

### Test 2 : Base Firmware (9/10/18)

Designing base firmware package on top of which prototyping will be done . Base firmwares are stored in 
```code
For ESP32 Devices fw_esp32.zip
For ESP8266 Devices fw_esp8266.zip

To Flash 
mos flash fw_esp32.zip
```
Features of base firmware
```code
1. Status LEDs status leds can be connected at GPIO 5 of both devices
2. Create AP and connect to STA
3. Show Wifi Connectivity status
```
#### Test Case 0 
Status LED blinking
led pin : 5
esp8266 - D1
esp32 - D5
```code
RPC : http://192.168.4.1/rpc/blink
POST Payload:

{
val:1
}

```
Val = 1 : trigger blink_once()
Val = 2 : trigger stop_blink()
Val = 3 : trigger start_blink() 

PASS

## TODO

### 1. Implement functionality to auto create a network structure , Module 3

All devices connect to backbone network when in centralized mode

```javascript
Cfg.set(wifi:{sta:{ssid:"Backbone",paswd:"password"}};
```

When backbone network goes down or when in chained mode , trigger a scan and connect to the IoT wifi SSID with largest signal 

```javascript

Wifi.scan(..)

```
### 2. Formulate Intercom and resource discovery/access architecture

Create flow chart of how requests can be handled/forwarded aand what to do on callbacks

### 3. Write Intercom functions

Write implementations for 
```code
on_request()
on_callback()
on_resource()

```

### 4. Formulate and Implement Algorithm for preventing DHCP assigned IP clashes ( 05/10/18 )
Background : All device's default IP in AP mode is 192.168.4.1 . When a client connects to AP an IP in range 192.168.4.2 to 192.168.4.100 is assigned to the client
Problem : When 2 devices connected to each other in 

```code
iot_1 -----:> iot_0  
```
both are having same dhcp range as well as IP , Need to reconfigure dynamically AP or at least manually write an RPC to restart AP with a dhcp range and IP
such that it dosent clash with any other device in the network .

```javascript

    Cfg.set({wifi:{ap:{
ssid:"iot_1",pass:"password",enable:true,ip:"192.168.4.50"
,gw:"192.168.4.50",dhcp_start:"192.168.4.51",dhcp_end:"192.168.4.100"}}});

```
Above code has to be dynamic through an RPC 

## Build It Yourself (05/10/18)

### Installing Mongoose
[![Installing Mongoose on Windows](https://img.youtube.com/vi/bDsqR6HBseY/0.jpg)](https://www.youtube.com/watch?v=bDsqR6HBseY "Installing Mongoose on Windows")

https://mongoose-os.com/software.html

On Linux :
```code
  sudo add-apt-repository ppa:mongoose-os/mos
  sudo apt-get update
  sudo apt-get install mos
  mos --help
  mos

```

### Building iot_blockchain firmware

```shell
git clone https://github.com/shiveshnavin/iot_blockchain.git
cd iot_blockchain
mos build --arch esp32

```

### Flashing and Running
```code
mos flash --arch esp32
mos console
```

### Prototyping over Custom OTA updates

To facilitate auto update and triggered lite OTA updates custom OTA client in implemented .
To perform OTA

```code
1. Create a Web Server using XAMPP or AMPS or LAMPP
2. Put in htdocs or public_html directory a file named worker.js 
3. Worker.js contains all firmware code except OTA part
```
Customize run.sh
Replace  url with the url of the worker.js file and the IP with the IP address of the device 
```shell
#!/bin/bash
curl -X POST \
  http://192.168.4.1/rpc/update \
  -H 'cache-control: no-cache' \
  -H 'postman-token: 6f54ee23-8f55-9bd7-96b7-1a9aedfdac1b' \
  -d '{
	"url":"http://192.168.4.2:8080/worker.js"
      }'
``` 

## Refrences

Helpful Conceptual Video , this is not how things work here though [ESP8266 NAT](https://www.youtube.com/watch?v=OM2FqnMFCLw)
https://www.devteam.space/blog/iot-blockchain-technology-uses-cases-overview/

## Team



