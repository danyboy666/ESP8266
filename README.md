# ESP8266

# thanks for https://cordobo.com/2300-flash-esp8266-01-with-arduino-uno/
# alot of this documentation comes from there.

# Documentation on how to flash and program an ESP820 WiFi module.

        ### ESP8266 PINOUT ###

                  RST------0        0----GPIO1  (TXD0)
                  ADC0-----0        0----GPIO3  (RXD0)
   (EN) or (CS)   ENABLE---0        0----GPIO4  (SCL)
          (WAKE)  GPIO16---0        0----GPIO4  (SDA)
          (SCLK)  GPIO14---0        0----GPIO0  (RXD1) 
          (MISO)  GPIO12---0        0----GPIO2  (TXD1)
          (MOSI)  GPIO13---0        0----GPIO15 (CS) (TXD2)
                  VCC------0        0----GND 


4.7K ohms pullup resistor on CS
4.7K ohms pulldown resistor on GPIO15
hook up RST to a tact switch with a 4.7K ohms pulldown resistor
hook up GPIO0 to a tact switch with a 4.7K ohms pullup resistor

NOTE: use an external 3.3v source to power the module

# to program
USAGE: hold reset switch and press FLASH switch

# Raspberry pi 

ESP TX to raspberry RX
ESP RX to raspberry TX
ESP GND to raspberry GND


#software

# Arduino IDE

You might need to point the java version to jre to actually be able to start arduino IDE

# Test the MODULE

- Open arduino and select the com port used by rpi.
- Click on monitor COM port

type in the command: 'AT' without the single quote and you should get an 'OK' response.

AT+GMR with return some values


# Python

pip install –upgrade esptool

or 

sudo pip install –upgrade esptool


download the lastest firmware for the controller https://micropython.org/download/#esp8266

save and rename as micropy.bin

cd /home/pi/.local/bin

enter flash mode and type in:

sudo python3 esptool.py --port /dev/ttyS0 --baud=115200 read_flash 0 0x200000 flash_original.bin

this will read the flash content and save it in the current folder


pi@raspberrypi:~/.local/bin $ sudo python3 esptool.py --port /dev/ttyS0 --baud=115200 read_flash 0 0x200000 flash_original.bin
esptool.py v3.0
Serial port /dev/ttyS0
Connecting........_____....._____....._____
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: 84:cc:a8:84:3d:df
Uploading stub...
Running stub...
Stub running...
2097152 (100 %)
2097152 (100 %)
Read 2097152 bytes at 0x0 in 268.1 seconds (62.6 kbit/s)...
Hard resetting via RTS pin...
pi@raspberrypi:~/.local/bin $ ls -lah
total 2,5M
drwxr-xr-x 2 pi   pi   4,0K nov 27 19:24 .
drwxr-xr-x 5 pi   pi   4,0K nov 27 18:06 ..
-rwxr-xr-x 1 pi   pi   5,8K nov 27 18:06 espefuse.py
-rw-r--r-- 1 pi   pi   4,9K nov 27 18:06 espefuse.pyc
-rwxr-xr-x 1 pi   pi    38K nov 27 18:06 espsecure.py
-rw-r--r-- 1 pi   pi    32K nov 27 18:06 espsecure.pyc
-rwxr-xr-x 1 pi   pi   182K nov 27 18:06 esptool.py
-rw-r--r-- 1 pi   pi   164K nov 27 18:06 esptool.pyc
-rw-r--r-- 1 root root 2,0M nov 27 19:24 flash_original.bin


flash_original.bin is the backup save it somewhere in case but if you made it here you should be ok for erase and write.

# ERASE THE MODULE

sudo python3 /home/pi/.local/bin/esptool.py --port /dev/ttyS0 erase_flash


pi@raspberrypi:~ $ sudo python3 /home/pi/.local/bin/esptool.py --port /dev/ttyS0 erase_flash
esptool.py v3.0
Serial port /dev/ttyS0
Connecting........_____....._____....._____....._____....._
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: 84:cc:a8:84:3d:df
Uploading stub...
Running stub...
Stub running...
Erasing flash (this may take a while)...
Chip erase completed successfully in 3.3s
Hard resetting via RTS pin...


# WRITE THE NEW FIRWARE

sudo python3 /home/pi/.local/bin/esptool.py --port /dev/ttyS0 --baud 115200 write_flash --flash_size=detect 0 micropy.bin --verify


esptool.py v3.0
Serial port /dev/ttyS0
Connecting........____
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: 84:cc:a8:84:3d:df
Uploading stub...
Running stub...
Stub running...
Configuring flash size...
Auto-detected Flash size: 4MB
Flash params set to 0x0040
Compressed 619828 bytes to 404070...
Wrote 619828 bytes (404070 compressed) at 0x00000000 in 39.8 seconds (effective 124.6 kbit/s)...
Hash of data verified.

Leaving...
Verifying just-written flash...
(This option is deprecated, flash contents are now always read back after flashing.)
Flash params set to 0x0040
Verifying 0x97534 (619828) bytes @ 0x00000000 in flash against micropy.bin...
-- verify OK (digest matched)
Hard resetting via RTS pin...


