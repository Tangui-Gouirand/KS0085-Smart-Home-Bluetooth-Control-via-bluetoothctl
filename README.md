# KS0085 Smart Home Bluetooth Control via bluetoothctl

## Table of contents :

1.  [Keyestudio Arduino CODE](#keyestudio-arduino-code)
    * 1.1 [Installation](#installation-keyestudio-arduino)
        * 1.1.1 [Keyestudio wiring](#keyestudio-wiring)
        * 1.1.2 [Arduino Installation](#arduino-installation)
    * 1.2 [Arduino Code](#arduino-code)
        * 1.2.1 [prerequisite](#prerequisite)
            * 1.2.1.1 [Download Arduino code](#download-arduino-code)
            * 1.2.1.2 [Download Arduino Libraries](#download-arduino-libraries)
                * 1.2.1.2.1 [Add Libraries to Arduino](#add-libraries-to-arduino)
        * 1.2.2 [Arduino code for bluetooth control](#arduino-code-for-bluetooth-control)
2.  [Bluetoothctl](#bluetoothctl)
    * 2.1 [Installation](#installation)
    * 2.2 [Checking before started](#checking)
        * 2.2.1 [Bluetooth Status](#checking-bluetooth-status)
    * 2.3 [Usage](#usage)
        * 2.3.1 [Search for bluetooth devices](#use-bluetoothctl)
        * 2.3.2 [Start a connection](#start-a-connection)
        * 2.3.3 [READ and WRITE](#read-and-write)
            * 2.3.3.1 [READ](#show-bluetooth-updates)
            * 2.3.3.2 [WRITE](#write-on-bluetooth-device)

# Keyestudio Arduino CODE

Official documentation for the assembly and wiring of the keyestudio connected house </br>
[here](/smart-home-doc.pdf)

## Installation Keyestudio Arduino

### Keyestudio wiring

### Arduino Installation
Download [arduino][arduino] open source software

![Github](https://img.shields.io/github/v/release/arduino/Arduino)

Detailed instructions for installation :

-  [Linux][Linux]
-  [macOS][macOS]
-  [Windows][Windows]


## Arduino Code

### prerequisite

#### Download Arduino code 
Download my arduino code 
[here](../../tree/main/Smart_home)

> The arduino code is complete, but in this example, we will just see the Bluetooth part.

#### Download Arduino Libraries 

Download liquidcrystal-i2c libraries directly from the Arduino website:</br>
[here](https://www.arduino.cc/reference/en/libraries/liquidcrystal-i2c/)</br>
or directly from my GitHub :</br>
[here](../../blob/main/Smart_home/libraries/LiquidCrystal_I2C-1.1.2.zip)

##### Add Libraries to Arduino

To install a new library into your Arduino:</br>
Open the Arduino IDE and click on the "Sketch" menu, then Include Library > Add Libraries (.zip).</br>
Then select the library from your files.</br>

## Arduino code for bluetooth control

```cpp
void loop() {
	if (Serial.available() > 0) {
    		val = Serial.read(); //the value that the remote sends to us
  	}
  	switch (val) {  //for bluetooth remote control / depending on what i get
    		case 'r'://if i get the character "r"  (fan on)
      			Serial.println("fan on (remote control)"); //print on remote screen
      			digitalWrite(7, LOW);
      			digitalWrite(6, HIGH);
      			val = "0"; //so that it doesn't loop
      			break;
    		case 's'://if i get the character "s"  (fan off)
      			Serial.println("fan off (remote control)"); //print on remote screen
      			digitalWrite(7, LOW);
      			digitalWrite(6, LOW);
      			val = "0"; //so that it doesn't loop
      			break;
 	}
}
```

# Bluetoothctl

## Installation

### Install bluez

```bash
  sudo apt-get install bluez
```

## Checking

### Checking Bluetooth Status

```console
YOURCOMPUTER:~$ sudo systemctl status bluetooth
```

#### If the Bluetooth service status is not active you will have to enable it first :
```console
YOURCOMPUTER:~$ sudo systemctl enable bluetooth
```
Then start the service
```console
YOURCOMPUTER:~$ sudo systemctl start bluetooth
```

## Usage


### Use bluetoothctl

```console
YOURCOMPUTER:~$ sudo bluetoothctl
```

### Search bluetooth devices

You can start a scan by using the scanning on/off command :
```bash 
[bluetooth]:# scan on
```
If you can't find the Bluetooth device you are looking for, make sure that your system Bluetooth is discoverable :
```bash
[bluetooth]# discoverable on
```

You can disable the scanner when the operation of scanning is complete :
```bash
[bluetooth]# scan off
```

Display bluetooth devices
```bash
[bluetooth]# devices
Device E6:C1:6C:23:27:A3 TEST1
Device E8:C1:8C:03:41:13 TEST2
Device 64:33:DB:92:B6:80 HMsoft
```

### Start a connection

Now is the time to log in using the mac address of the desired device :

```bash
[bluetooth]#connect 64:33:DB:92:B6:80
```
> Note that once you are connected, the target is not bluetooth anymore but your localname tag.

### Read and Write

Now you can enter the [gatt menu][gatt_menu] :

```bash
[HMsoft]#menu gatt
```

show the different services and characteristics :

```bash
[HMsoft]#list-attributes 64:33:DB:92:B6:80
```

Enable the right properties for to acquire notification and writing :

```bash
[HMsoft]#select-atribute /org/bluez/hci0/dev_64_33_DB_92_B6_80/service0010/char0011
```

#### show bluetooth updates

```bash
[HMsoft:/service0010/char0011]#read
[HMsoft:/service0010/char0011]#notify on
```
You can disable the notify with the following command :

```bash
[HMsoft:/service0010/char0011]#notify off
```


#### Write on bluetooth device

```bash
[HMsoft:/service0010/char0011]#write 0x72
[HMsoft:/service0010/char0011]#write 0x73 0x0a
```
> 0x72 corresponds to character "r" </br>
> 0x73 0xda corresponds to character "s"

| Character | Hexadecimal | Function                                        |
|-----------|-------------|-------------------------------------------------|
| a         | 0x61        | Sends a high value to digital pin 13           |
| b         | 0x62        | Sends a low value to digital pin 13            |
| c         | 0x63        | Sends a high value to digital pin 12           |
| d         | 0x64        | Sends a low value to digital pin 12            |
| e         | 0x65        | Launches music 1                                |
| f         | 0x66        | Launches music 2                                |
| g         | 0x67        | Stops music                                   |
| h         | 0x68        | Returns brightness value                         |
| i         | 0x69        | Returns gas value                               |
| j         | 0x6A        | Returns soil moisture value                     |
| k         | 0x6B        | Returns water value                             |
| l         | 0x6C        | Opens door                                     |
| m         | 0x6D        | Closes door                                    |
| n         | 0x6E        | Closes window                                  |
| o         | 0x6F        | Opens window                                   |
| p         | 0x70        | Interior yellow LED on                           |
| q         | 0x71        | Interior yellow LED off                          |
| r         | 0x72        | Fan on                                        |
| s         | 0x73 0x0A   | Fan off (with newline)                          |
| .         | 0x2E        | Sends "_" to door password                     |
| _         | 0x5F        | Sends "." to door password                     |
| v         | 0x76        | Validates door code                            |

<br></br>

[Linux]: https://www.arduino.cc/en/Guide/Linux
[macOS]: https://www.arduino.cc/en/Guide/macOS
[Windows]: https://www.arduino.cc/en/Guide/Windows
[arduino]: https://www.arduino.cc/en/Main/Software
[gatt_menu]: https://www.bluetooth.com/specifications/specs/gatt-specification-supplement-6/
```
