nrfutil
==========
nrfutil is a Python package that includes the nrfutil command line utility and the nordicsemi library.

This project is a branch of the nrfutil project based on the original version of the license, which allowed use on hardware other than Nordic Semiconductor.

This utility is intended to be used with the Secure Device Update library (SDU), which uses the same download protocol used by Nordic tools to update other hardware devices.  The SDU project has no affiliation with Nordic Semiconductor.  The SDU project and corresponding instructions below which reference use of Nordic tools are not supported or endorced by Nordic Semiconductor.


About
-----
The library is written for Python 2.7.

Versions
--------
The original nordic nrfutil project supports 2 different and incompatible DFU package formats

* legacy: used a simple structure and no security
* modern: uses Google's protocol buffers for serialization and can be cryptographically signed

The SDU project assumes a modern implementation with google protocol buffers for serialization and cryptographic signing

Prerequisites
-------------
To install nrfutil the following prerequisites must be satisfied:

* Python 2.7 (2.7.6 or newer, not Python 3)
* pip (https://pip.pypa.io/en/stable/installing.html)
* setuptools (upgrade to latest version: pip install -U setuptools)
* install required modules: pip install -r requirements.txt

py2exe prerequisites (Windows only):  

* py2exe (Windows only) (v0.6.9) (pip install http://sourceforge.net/projects/py2exe/files/latest/download?source=files)
* VC compiler for Python (Windows only) (http://www.microsoft.com/en-us/download/confirmation.aspx?id=44266)

Installation
------------
To install the library to the local Python site-packages and script folder:  
```
python setup.py install
```

To generate a self-contained Windows exe version of the utility (Windows only):  
```
python setup.py py2exe
```
NOTE: Some anti-virus programs will stop py2exe from executing correctly when it modifies the .exe file.

Usage
-----
To get info on usage of nrfutil:  
```
nrfutil --help
```

### Getting Started with SDU
This section provides an overview of the use of this tool to demonstrate OTA firmware download over Bluetooth BLE using the SDU library and Nordic tools with a [Redbear Duo](https://github.com/redbear/Duo) target device, providing for Bluetooth over the air firmware updates on your Duo.

This project is not sponsored or endorsed by Redbear or Nordic Semiconductor.  All Redbear related trademark names including Duo are owned by Redbear.  All Nordic related trademark names including nrfutil and nRF Toolkit are owned by Nordic Semiconductor

The instructions assume you've configured your Redbear Duo for use with Arduino based on the instructions in the [Redbear Duo Getting Started with Arduino IDE](https://github.com/redbear/Duo/blob/master/docs/getting_started_with_arduino_ide.md) guide.

The instructions also assume you've installed the [dfu-util](https://github.com/redbear/Duo/blob/master/docs/dfu-util_installation_guide.md) utility from Redbear for device firmware update.

The instructions make use of a predefined [test_key](redbear_duo/test_key) private key, two SDU sample signed firmware binaries [BLE_BlinkFast.zip](redbear_duo/BLE_BlinkFast.zip) and [BLE_BlinkSlow.zip](redbear_duo/BLE_BlinkSlow.zip), as well as the binary payload in [BLE_Blink_Fast.bin](redbear_duo/BLE_Blink_Fast.bin).  Each of the BLE files are built based on the [SimpleBLEPeripheral](https://github.com/Trellis-Logic/STM32-Arduino/tree/master/arduino/libraries/RedBear_Duo/examples/03.BLE/SimpleBLEPeripheral) Redbear Duo example code with some integration of the SDU library

#### Download the Initial SDU Example bin file to the DUO

In this section, we'll setup your Duo with the initial file it needs to be able to support Bluetooth OTA using the nRF Toolbox application
1. Put your duo in DFU mode
 * Hold down buth buttons
 * Release only the RESET button, while holding down the SETUP button.
 * Wait for the LED to start blinkign yellow
 * Release the SETUP button
2. Using dfu-util, download the .bin file to the device, using command
```dfu-util -d 2b04:d058 -a 0 -s 0x80C0000 -D redbear_duo/BLE_Blink_Fast.bin```
3. Reset the duo.  You should now see the blue LED flashing about 10 times per second.

#### Upload SDU Firmware via the nRF Toolbox

1. Install nRF-Toolbox for android or iphone from https://www.nordicsemi.com/eng/Products/Nordic-mobile-Apps/nRF-Toolbox-App
2. Download the two signed firmware binaries [BLE_BlinkFast.zip](redbear_duo/BLE_BlinkFast.zip) and [BLE_BlinkSlow.zip](redbear_duo/BLE_BlinkSlow.zip), as well as the binary payload in [BLE_Blink_Fast.bin](redbear_duo/BLE_Blink_Fast.bin) to your phone.  You may want to email them to yourself.
3. Open the nRF-Toolbox and select the "DFU" button
4. Select the payload zip file [BLE_BlinkSlow.zip](redbear_duo/BLE_BlinkSlow.zip)
5. Select the Duo.  It should be listed as "RBL-DUO"
6. Click upload.  The payload should transfer to the device successfully.  After a few seconds, the LED should start to blink more slowly (approximately once per second) indicating a new firmware payload is now in use.
7. If desired, repeat the steps to switch between the [BLE_BlinkFast.zip](redbear_duo/BLE_BlinkFast.zip) and [BLE_BlinkSlow.zip](redbear_duo/BLE_BlinkSlow.zip) payloads.

#### Sign Your Own Firmware Image

In this section, we'll sign your own firmware image with the [test_key](redbear_duo/test_key) used by the SDU example firmware.  This will allow your firmware image to be transferred successfully over bluetooth and programmed on the redbear Duo.  Note that since we haven't yet integrated the SDU library with your application, you will need to perform future updates using the dfu-util or Arduino environment.  This first test just verifies your download file can be programmed successfully using the sample firmware application

1. Export your sketch in Arduino using Sketch->Export Compiled Binary
2. Sign your sketch binary with the test key using
```nrfutil pkg generate --application <path_to_bin_file> --key-file redbear_duo/test_key myapplication.zip```
3. Use the instructions above with the nRF Toolbox application to download the firmware image to the Redbear Duo

### Commands
There are several commands that you can use to perform different tasks related to DFU:

#### pkg
This set of commands allow you to generate a package for Device Firmware Update.
#
#### generate
Generate a package (.zip file) that you can later use with a mobile application or any other means to update the firmware of an nRF5x IC over the air. This command takes several options that you can list using:
```
nrfutil pkg generate --help
```
Below is an example of the generation of a package from an application's ```app.hex``` file:
```
nrfutil pkg generate --application app.hex --key-file key.pem app_dfu_package.zip
```
#### dfu
This set of commands allow you to perform an actual firmware update over a serial or BLE connection.
#
##### serial
Perform a full DFU procedure over a serial (UART) line. This command takes several options that you can list using:
```
nrfutil dfu serial --help
```
Below is an example of the execution of a DFU procedure of the file generated above over COM3 at 115200 bits per second:
```
nrfutil dfu serial -pkg app_dfu_package.zip -p COM3 -b 115200
```
#### keys
This set of commands allow you to generate and display cryptographic keys used to sign and verify DFU packages.
##### generate
Generate a private (signing) key and store it in a file in PEM format.
The following will generate a private key and store it in a file named ```private.pem```:
```
nrfutil keys generate private.pem
```
##### display
Display a private (signing) or public (verification) key from a PEM file taken as input. This command takes several options that you can list using:
```
nrfutil keys display --help
```
Below is an example of displaying a public key in code format from the key file generated above:
```
nrfutil keys display --key pk --format code private.pem
```
#### version
This command displays the version of nrfutil.
