nrfutil
==========
nrfutil is a Python package that includes the nrfutil command line utility and the nordicsemi library.

This project is a branch of the nrfutil project based on the original version of the license, which allowed use on hardware other than Nordic Semiconductor.

This utility is intended to be used with the Secure Device Update library (SDU), which uses the same download protocol used by Nordic tools to update other hardware devices.  The SDU project has no affiliation with Nordic Semiconductor.  The SDU project and corresponding instructions below which reference use of Nordic tools are not supported or endorced by Nordic Semiconductor.

For more information about the Secure Device Update Project, see [the project page](https://trellis-logic.github.io/sdu/).  For information about use of the SDU library with the RedBear Duo platform, see [the RedBear Duo platform page](https://trellis-logic.github.io/sdu/platforms/redbear_duo/platform.html)


About
-----
The library is written for Python 2.7.

Versions
--------
The original nordic nrfutil project supports 2 different and incompatible DFU package formats

* legacy: used a simple structure and no security
* modern: uses Google's protocol buffers for serialization and can be cryptographically signed

The [SDU project](https://trellis-logic.github.io/sdu) supports only the modern implementation with google protocol buffers for serialization and cryptographic signing

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
