---
title: "Bad_USB"
date: 2024-09-05T17:38:49-07:00
draft: false
---

# Bad USB

A few years ago I wanted to play around with the [Hak5 Rubber Ducky](https://shop.hak5.org/products/usb-rubber-ducky), but I could not justify to my wife the need for this ~$80 toy(at lest at the time) as she would see it. So I looked around for a bit and around that time on YouTube "The Cyber Mentor" posted [this](https://youtu.be/uH-4btjE56E?si=vHVjLlnpdBxSCBDd) video on a $2 Rubber Ducky(I think they are closer to $4 now). This cost has many advantages. In the video, Heath used a Digispark ATtiny85. 
![[ATtiny85.png.png]]

So I got a five pack and tried to follow along. I already had the Arduino IDE, I was using it for other projects I was doing a few years before to make an auto watering system for a tomato plants. 
So I start to try to write the firmware to find I can't find the chip. After a few hours I got that sorted and then moved on to uploading the file and the link given did not work. I could not find why the link was dead, hear you try "http://digistump.com/package_digistump_index.json". So I set it aside and forgot about it. Now a few years later I was cleaning and found the stack of chips. I also was encouraged to buy a device that had this and other functionality and I decided I could use a few hours to try to find a solution to this problem. 
### Sources that helped out with this project
 So I looked to see if anyone had found a solution to get the package index for this microcontroller and I found this [article] with a [link](https://www.best-microcontroller-projects.com/support-files/package_digistump_index_json.txt) file is below. In the article it tells you that you need to save the file to your computer and put the path into your Arduino IDE go to File -> Preferences under Additional Boards Manager URLs: put  "file:///C:/Users/YourUsername/Desktop/package_digistump_index.json"
![board-json.png](/bad_usb/board-json.png)

then under Tools -> Boards -> Board Manager search for "digistump" and install "Digistump AVR Boards"
![board-manager.png](/bad_usb/board-manager.png)

Now we can test with a fun notepad message. 

```C
#include "DigiKeyboard.h"

void setup() {
  // put your setup code here, to run once:
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.delay(500);
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);
  DigiKeyboard.delay(500);
  DigiKeyboard.print("notepad");
  DigiKeyboard.delay(500);
  DigiKeyboard.sendKeyStroke(KEY_ENTER);
}

void loop() {
  // put your main code here, to run repeatedly:
  DigiKeyboard.delay(500);
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.println("Hello!");
  DigiKeyboard.delay(500);
  DigiKeyboard.sendKeyStroke(KEY_ENTER);
}
```

#### Note
When you put the script on the board you will need to start the compile and upload process as you would usually do and then plug in the ATtiny85 to initialize USB detection and you should see the message say it is uploading. 

![upload.png](/bad_usb/upload.png)


Then for fun I made a rick roll that also opens my website
```c
#include "DigiKeyboard.h"

void setup() {
  // put your setup code here, to run once:
  DigiKeyboard.delay(2000);
  DigiKeyboard.sendKeyStroke(0);
}

void loop() {
  // put your main code here, to run repeatedly:
  DigiKeyboard.delay(2000);
  DigiKeyboard.sendKeyStroke(0);
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);
  DigiKeyboard.delay(600);
  DigiKeyboard.print("https://jon112358.com/pages/about");
  DigiKeyboard.sendKeyStroke(KEY_ENTER);
  DigiKeyboard.delay(5000);
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);
  DigiKeyboard.delay(3000);
  DigiKeyboard.print("https://youtu.be/dQw4w9WgXcQ?t=43s");
  DigiKeyboard.sendKeyStroke(KEY_ENTER);
  DigiKeyboard.delay(2000);
  //DigiKeyboard.sendKeyStroke(KEY_F11);
  for(;;){ /*empty*/ }
}
```

As you see both are for windows and only doing simple proof of concept scripts below you will find a few links to some other scripts I found on GitHub. 
Note: I was having issues with Verify, and Upload when I left the "void setup" empty.

## My next steps 
I plan to write a Linux version. I will assume default Ubuntu keybindings, with Gnome installed. I will need to document for if they are using non-standard programs. 
I would like to also find how to write for macOS if I can find a system to test on. 
Also to reduce run time and to run in the background, all systems. 

Maybe a Raspberry PI Zero version that I can edit on the spot. 

## The file that I needed 
```json   package_digistump_index.json
{
   "packages":[
      {
         "name":"digistump",
         "maintainer":"Digistump",
         "websiteURL":"http://digistump.com",
         "email":"support@digistump.com",
         "help":{
            "online":"https://digistump.com/board"
         },
         "platforms":[
         {
          "name": "Digistump AVR Boards",
          "architecture": "avr",
          "version": "1.6.7",
          "category": "Digistump",
          "url": "https://github.com/digistump/DigistumpArduino/releases/download/1.6.7/digistump-avr-1.6.7.zip",
          "archiveFileName": "digistump-avr-1.6.7.zip",
          "checksum": "SHA-256:7F7E9F5AB982163F7A792C411326F0192A35B8A90890B7934F2F424A5426583D",
          "size": "2491070",
          "help": {
            "online": "https://github.com/digistump/DigistumpArduino/issues"
          },
          "boards": [
            {
              "name": "Digispark (Default - 16.5mhz)"
            },
            {
              "name": "Digispark Pro (Default 16 Mhz)"
            },
            {
              "name": "Digispark Pro (16 Mhz) (32 byte buffer)"
            },
            {
              "name": "Digispark Pro (16 Mhz) (64 byte buffer)"
            },
            {
              "name": "Digispark (16mhz - No USB)"
            },
            {
              "name": "Digispark (8mhz - No USB)"
            },
            {
              "name": "Digispark (1mhz - No USB)"
            }
          ],
          "toolsDependencies": [
            {
              "packager": "arduino",
              "name": "avr-gcc",
              "version": "4.8.1-arduino5"
            },
            {
              "packager": "digistump",
              "name": "micronucleus",
              "version": "2.0a4"
            }
          ]
        },
        {
          "name": "Digistump SAM Boards (32-bits ARM Cortex-M3)",
          "architecture": "sam",
          "version": "1.6.7",
          "category": "Digistump",
          "url": "https://github.com/digistump/DigistumpArduino/releases/download/1.6.7/digistump-sam-1.6.7.zip",
          "archiveFileName": "digistump-sam-1.6.7.zip",
          "checksum": "SHA-256:C091CA9372220E18394AD7876A919201705BB281167309A2571270E374DB2698",
          "size": "21937559",
          "help": {
            "online": "https://github.com/digistump/DigistumpArduino/issues"
          },
          "boards": [
            {"name": "Digistump DigiX"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "micronucleus",
              "version": "2.0a4"
            },
            {
              "packager": "digistump",
              "name": "arm-none-eabi-gcc",
              "version": "4.8.3-2014q1"
            },
            {
              "packager": "digistump",
              "name": "bossac",
              "version": "1.3a-arduino"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.0",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/releases/download/1.0.0/core-1.0.0.zip",
          "archiveFileName": "core-1.0.0.zip",
          "checksum": "SHA-256:631C214AA1D6CD01794A47D53541433CE5AEE59159627A559E4293F78B3B8E45",
          "size": "6879430",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.0"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump",
              "version": "1.20.0-26-gb404fb9-2",
              "name": "xtensa-lx106-elf-gcc"
            },
            {
              "packager": "digistump",
              "version": "0.1.2",
              "name": "mkspiffs"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.1",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/releases/download/1.0.1/core-1.0.1.zip",
          "archiveFileName": "core-1.0.1.zip",
          "checksum": "SHA-256:64A3EE39C91AB6BD7BE05685405F7D45A70A2B0E227B4DF930E9D8623B83CF39",
          "size": "6881865",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Manual Config Only)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.1"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump",
              "version": "1.20.0-26-gb404fb9-2",
              "name": "xtensa-lx106-elf-gcc"
            },
            {
              "packager": "digistump",
              "version": "0.1.2",
              "name": "mkspiffs"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.2",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/releases/download/1.0.2/core-1.0.2.zip",
          "archiveFileName": "core-1.0.2.zip",
          "checksum": "SHA-256:988EEC1A3E4B97A0F31332F4A8FF7AB0E30515EB5DAC22D96DB3B0130213DAF8",
          "size": "6945915",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Manual Config Only)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.2"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump", 
              "version": "1.20.0-26-gb404fb9-2", 
              "name": "xtensa-lx106-elf-gcc"
            }, 
            {
              "packager": "digistump", 
              "version": "0.1.2", 
              "name": "mkspiffs"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.3",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/releases/download/1.0.3/core-1.0.3.zip",
          "archiveFileName": "core-1.0.3.zip",
          "checksum": "SHA-256:AD14574F6DD9085C0874DCE255038F3C0C9F5417221F960ECD80E17E5AF063BE",
          "size": "6947972",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Manual Config Only)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.2"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump",
              "version": "1.20.0-26-gb404fb9-2", 
              "name": "xtensa-lx106-elf-gcc"
            }, 
            {
              "packager": "digistump", 
              "version": "0.1.2", 
              "name": "mkspiffs"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.4",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/releases/download/1.0.4/core-1.0.4.zip",
          "archiveFileName": "core-1.0.4.zip",
          "checksum": "SHA-256:2ABFE8ABB54223032E3B3B852A869D2518AD4BBDC7119942D45B108A04F877EB",
          "size": "6947979",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Manual Config Only)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.2"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump",
              "version": "1.20.0-26-gb404fb9-2",
              "name": "xtensa-lx106-elf-gcc"
            },
            {
              "packager": "digistump",
              "version": "0.1.2",
              "name": "mkspiffs"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.5",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/releases/download/1.0.5/core-1.0.5.zip",
          "archiveFileName": "core-1.0.5.zip",
          "checksum": "SHA-256:93FC1BFCC56DCCF7B6858DBB81F7B22D5C252B444947F57CCB898C62CE3AE96D",
          "size": "6947985",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Manual Config Only)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.2"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump",
              "version": "1.20.0-26-gb404fb9-2", 
              "name": "xtensa-lx106-elf-gcc"
            }, 
            {
              "packager": "digistump",
              "version": "0.1.2",
              "name": "mkspiffs"
            }
          ]
        },
        {
          "name": "Oak by Digistump",
          "architecture": "oak",
          "version": "1.0.6",
          "category": "Digistump",
          "url": "https://github.com/digistump/OakCore/archive/1.0.6.zip",
          "archiveFileName": "1.0.6.zip",
          "checksum": "SHA-256:47071BB74FEEBA7F4BE7623684F3E5AB06F19F7476B00F9C1CFA6ADA45084797",
          "size": "6861729",
          "help": {
            "online": "http://digistump.com/wiki/oak"
          },
          "boards": [
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Default)"},
            {"name": "Oak by Digistump (Pin 1 Safe Mode - Manual Config Only)"},
            {"name": "Oak by Digistump (No Safe Mode - ADVANCED ONLY)"}
          ],
          "toolsDependencies": [
            {
              "packager": "digistump",
              "name": "oakcli",
              "version": "1.0.2"
            },
            {
              "packager": "digistump",
              "name": "esptool2",
              "version": "0.9.1"
            },
            {
              "packager": "digistump", 
              "version": "1.20.0-26-gb404fb9-2", 
              "name": "xtensa-lx106-elf-gcc"
            }, 
            {
              "packager": "digistump", 
              "version": "0.1.2", 
              "name": "mkspiffs"
            }
          ]
        }

         ],
         "tools":[
            {
             "name": "micronucleus",
             "version": "2.0a4",
             "systems": [
               {
                 "host": "x86_64-apple-darwin",
                 "archiveFileName": "micronucleus-2.0a4-osx.tar.gz",
                 "url": "https://github.com/digistump/DigistumpArduino/releases/download/1.6.5a/micronucleus-2.0a4-osx.tar.gz",
                 "checksum": "SHA-256:B5EB0C7B251CD88F4816186BB931855834141E71A28D90FB9E46788E483AA421",
                 "size": "51203"
               },
               {
                 "host": "i686-mingw32",
                 "archiveFileName": "micronucleus-2.0a4-win.zip",
                 "url": "https://github.com/digistump/DigistumpArduino/releases/download/1.6.5a/micronucleus-2.0a4-win.zip",
                 "checksum": "SHA-256:7027971118FDE88484AACB5D8CA7867D66E273839EC3B2592616829317BB70E4",
                 "size": "1712005"
               },
               {
                 "host": "i686-linux-gnu",
                 "archiveFileName": "micronucleus-2.0a4-linux32.tar.gz",
                 "url": "https://github.com/digistump/DigistumpArduino/releases/download/1.6.5a/micronucleus-2.0a4-linux32.tar.gz",
                 "checksum": "SHA-256:0D4286388EED28D1ECB29AFE81253F24F54D4F0A5C1B2C17507EABD504C595F8",
                 "size": "21909"
               },
               {
                 "host": "x86_64-linux-gnu",
                 "archiveFileName": "micronucleus-2.0a4-linux64.tar.gz",
                 "url": "https://github.com/digistump/DigistumpArduino/releases/download/1.6.5a/micronucleus-2.0a4-linux64.tar.gz",
                 "checksum": "SHA-256:1F545C0BB60E85A604901C2D7044772AC91776594C209C571DFEDAD4A70195B8",
                 "size": "22874"
               }

             ]
           },
           {
          "name": "arm-none-eabi-gcc",
          "version": "4.8.3-2014q1",
          "systems": [
            {
              "host": "i686-mingw32",
              "archiveFileName": "gcc-arm-none-eabi-4.8.3-2014q1-windows.tar.gz",
              "url": "http://downloads.arduino.cc/gcc-arm-none-eabi-4.8.3-2014q1-windows.tar.gz",
              "checksum": "SHA-256:fd8c111c861144f932728e00abd3f7d1107e186eb9cd6083a54c7236ea78b7c2",
              "size": "84537449"
            },
            {
              "host": "x86_64-apple-darwin",
              "url": "http://downloads.arduino.cc/gcc-arm-none-eabi-4.8.3-2014q1-mac.tar.gz",
              "archiveFileName": "gcc-arm-none-eabi-4.8.3-2014q1-mac.tar.gz",
              "checksum": "SHA-256:3598acf21600f17a8e4a4e8e193dc422b894dc09384759b270b2ece5facb59c2",
              "size": "52518522"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "http://downloads.arduino.cc/gcc-arm-none-eabi-4.8.3-2014q1-linux64.tar.gz",
              "archiveFileName": "gcc-arm-none-eabi-4.8.3-2014q1-linux64.tar.gz",
              "checksum": "SHA-256:d23f6626148396d6ec42a5b4d928955a703e0757829195fa71a939e5b86eecf6",
              "size": "51395093"
            },
            {
              "host": "i686-pc-linux-gnu",
              "url": "http://downloads.arduino.cc/gcc-arm-none-eabi-4.8.3-2014q1-linux32.tar.gz",
              "archiveFileName": "gcc-arm-none-eabi-4.8.3-2014q1-linux32.tar.gz",
              "checksum": "SHA-256:ba1994235f69c526c564f65343f22ddbc9822b2ea8c5ee07dd79d89f6ace2498",
              "size": "51029223"
            }
          ]
        },
        {
          "name": "bossac",
          "version": "1.3a-arduino",
          "systems": [
            {
              "host": "i686-linux-gnu",
              "url": "http://downloads.arduino.cc/tools/bossac-1.3a-arduino-i686-linux-gnu.tar.bz2",
              "archiveFileName": "bossac-1.3a-arduino-i686-linux-gnu.tar.bz2",
              "checksum": "SHA-256:d6d10362f40729a7877e43474fcf02ad82cf83321cc64ca931f5c82b2d25d24f",
              "size": "147359"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "http://downloads.arduino.cc/tools/bossac-1.3a-arduino-x86_64-pc-linux-gnu.tar.bz2",
              "archiveFileName": "bossac-1.3a-arduino-x86_64-pc-linux-gnu.tar.bz2",
              "checksum": "SHA-256:c1daed033251296768fa8b63ad283e053da93427c0f3cd476a71a9188e18442c",
              "size": "26179"
            },
            {
              "host": "i686-mingw32",
              "url": "http://downloads.arduino.cc/tools/bossac-1.3a-arduino-i686-mingw32.tar.bz2",
              "archiveFileName": "bossac-1.3a-arduino-i686-mingw32.tar.bz2",
              "checksum": "SHA-256:a37727622e0f86cb4f2856ad0209568a5d804234dba3dc0778829730d61a5ec7",
              "size": "265647"
            },
            {
              "host": "i386-apple-darwin11",
              "url": "http://downloads.arduino.cc/tools/bossac-1.3a-arduino-i386-apple-darwin11.tar.bz2",
              "archiveFileName": "bossac-1.3a-arduino-i386-apple-darwin11.tar.bz2",
              "checksum": "SHA-256:40770b225753e7a52bb165e8f37e6b760364f5c5e96048168d0178945bd96ad6",
              "size": "39475"
            }
          ]
        },
        {
          "name": "oakcli",
          "version": "1.0.2",
          "systems": [
            {
              "host": "i686-linux-gnu",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.2/oakcli-1.0.2-linux32.tar.gz",
              "archiveFileName": "oakcli-1.0.2-linux32.tar.gz",
              "checksum": "SHA-256:C59996CC95614BA4041CFB1CC0F34F3064CA6FCF6887970A6D5E61A5B100ED71",
              "size": "5684530"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.2/oakcli-1.0.2-linux64.tar.gz",
              "archiveFileName": "oakcli-1.0.2-linux64.tar.gz",
              "checksum": "SHA-256:269774B42F87D1E2F3E197FD40B87971AF4ACA91DDFDA427234AEAA3DF137284",
              "size": "5899396"
            },
            {
              "host": "i686-mingw32",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.2/oakcli-1.0.2-win32.zip",
              "archiveFileName": "oakcli-1.0.2-win32.zip",
              "checksum": "SHA-256:82192B93736771F804EF30258E9A806F0B0A6B02C3ED6F11356E169C08948D66",
              "size": "3230824"
            },
            {
              "host": "i386-apple-darwin11",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.2/oakcli-1.0.2-osx.tar.gz",
              "archiveFileName": "oakcli-1.0.2-osx.tar.gz",
              "checksum": "SHA-256:A4992CFAC828D23830F3C24FE52FD678E553FB17C7C8B9232DC39CFF06E6C3B4",
              "size": "4562442"
            }
          ]
        },
        {
          "name": "oakcli",
          "version": "1.0.1",
          "systems": [
            {
              "host": "i686-linux-gnu",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.1/oakcli-1.0.1-linux32.tar.gz",
              "archiveFileName": "oakcli-1.0.1-linux32.tar.gz",
              "checksum": "SHA-256:4050FBF159A4473E69DF9949236855E8EAD28D4379122DFD608E3F0AB2BAA13D",
              "size": "5688406"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.1/oakcli-1.0.1-linux64.tar.gz",
              "archiveFileName": "oakcli-1.0.1-linux64.tar.gz",
              "checksum": "SHA-256:575256152F5F6483E34A42F900E3EC1FF2A779578C94A03001EBC746013E1B4B",
              "size": "5905476"
            },
            {
              "host": "i686-mingw32",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.1/oakcli-1.0.1-win32.zip",
              "archiveFileName": "oakcli-1.0.1-win32.zip",
              "checksum": "SHA-256:82CEA3AC89CC604F65141C27B08E2CD0574CF24786FBB5584B042ECCEE54852B",
              "size": "3234116"
            },
            {
              "host": "i386-apple-darwin11",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.1/oakcli-1.0.1-osx.tar.gz",
              "archiveFileName": "oakcli-1.0.1-osx.tar.gz",
              "checksum": "SHA-256:6564B415AF1235A9A68ED1AC0AA5342997057250B12CAFB1CE8DD9BFE4F587D9",
              "size": "4565595"
            }
          ]
        },
        {
          "name": "oakcli",
          "version": "1.0.0",
          "systems": [
            {
              "host": "i686-linux-gnu",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.0/oakcli-1.0.0-linux32.tar.gz",
              "archiveFileName": "oakcli-1.0.0-linux32.tar.gz",
              "checksum": "SHA-256:234336092B287531E8143CAA06232F582424B49C193F19B1C7F0D85186F29ABB",
              "size": "5688186"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.0/oakcli-1.0.0-linux64.tar.gz",
              "archiveFileName": "oakcli-1.0.0-linux64.tar.gz",
              "checksum": "SHA-256:236F3F0A8CA77CDD7F58708A6292F9295E0B89EA33930FF6C108860EF68BD55B",
              "size": "5905721"
            },
            {
              "host": "i686-mingw32",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.0/oakcli-1.0.0-win32.zip",
              "archiveFileName": "oakcli-1.0.0-win32.zip",
              "checksum": "SHA-256:07D57550C7CF225D3CF6CC2CBA4089DA75FB79F9F7DA5CF41F0C882B89EA0DE9",
              "size": "3233023"
            },
            {
              "host": "i386-apple-darwin11",
              "url": "https://github.com/digistump/OakCLI/releases/download/1.0.0/oakcli-1.0.0-osx.tar.gz",
              "archiveFileName": "oakcli-1.0.0-osx.tar.gz",
              "checksum": "SHA-256:6685772356743F7ABFEB516D25A8C62B427EA0AFDE2402BE39C09D0D0AF33B67",
              "size": "4561638"
            }
          ]
        },
        {
          "name": "esptool2",
          "version": "0.9.1",
          "systems": [
            {
              "host": "i686-linux-gnu",
              "url": "https://github.com/digistump/OakCore/releases/download/0.9.2/esptool2-0.9.1-linux32.tar.gz",
              "archiveFileName": "esptool2-0.9.1-linux32.tar.gz",
              "checksum": "SHA-256:DEA66398D035E44BA9D29473EABADB055A304E4FCA2C7C5F90CE31EDA114AED6",
              "size": "8577"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "https://github.com/digistump/OakCore/releases/download/0.9.2/esptool2-0.9.1-linux64.tar.gz",
              "archiveFileName": "esptool2-0.9.1-linux64.tar.gz",
              "checksum": "SHA-256:6D54579A5B2C7F9910916A1F22C87589E4563B7FC49A176EAA7A57918A81CB2B",
              "size": "8667"
            },
            {
              "host": "i686-mingw32",
              "url": "https://github.com/digistump/OakCore/releases/download/0.9.2/esptool2-0.9.1-win32.zip",
              "archiveFileName": "esptool2-0.9.1-win32.zip",
              "checksum": "SHA-256:648772172E9895FA12502EF0152EFA1176775949CE9AF8E5E0D989BA774C2E14",
              "size": "7859"
            },
            {
              "host": "i386-apple-darwin11",
              "url": "https://github.com/digistump/OakCore/releases/download/0.9.2/esptool2-0.9.1-osx.tar.gz",
              "archiveFileName": "esptool2-0.9.1-osx.tar.gz",
              "checksum": "SHA-256:A30EAF00102DF2BE9DEF9CDC0320F116C6A3629069917C9537A37871ADC09AC1",
              "size": "6746"
            }
          ]
        },
        {
          "name": "xtensa-lx106-elf-gcc",
          "version": "1.20.0-26-gb404fb9-2",
          "systems": [
            {
              "host": "i686-mingw32",
              "url": "http://arduino.esp8266.com/win32-xtensa-lx106-elf-gb404fb9-2.tar.gz",
              "archiveFileName": "win32-xtensa-lx106-elf-gb404fb9-2.tar.gz",
              "checksum": "SHA-256:10476b9c11a7a90f40883413ddfb409f505b20692e316c4e597c4c175b4be09c",
              "size": "153527527"
            },
            {
              "host": "x86_64-apple-darwin",
              "url": "http://arduino.esp8266.com/osx-xtensa-lx106-elf-gb404fb9-2.tar.gz",
              "archiveFileName": "osx-xtensa-lx106-elf-gb404fb9-2.tar.gz",
              "checksum": "SHA-256:0cf150193997bd1355e0f49d3d49711730035257bc1aee1eaaad619e56b9e4e6",
              "size": "35385382"
            },
            {
              "host": "i386-apple-darwin",
              "url": "http://arduino.esp8266.com/osx-xtensa-lx106-elf-gb404fb9-2.tar.gz",
              "archiveFileName": "osx-xtensa-lx106-elf-gb404fb9-2.tar.gz",
              "checksum": "SHA-256:0cf150193997bd1355e0f49d3d49711730035257bc1aee1eaaad619e56b9e4e6",
              "size": "35385382"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "http://arduino.esp8266.com/linux64-xtensa-lx106-elf-gb404fb9.tar.gz",
              "archiveFileName": "linux64-xtensa-lx106-elf-gb404fb9.tar.gz",
              "checksum": "SHA-256:46f057fbd8b320889a26167daf325038912096d09940b2a95489db92431473b7",
              "size": "30262903"
            },
            {
              "host": "i686-pc-linux-gnu",
              "url": "http://arduino.esp8266.com/linux32-xtensa-lx106-elf.tar.gz",
              "archiveFileName": "linux32-xtensa-lx106-elf.tar.gz",
              "checksum": "SHA-256:b24817819f0078fb05895a640e806e0aca9aa96b47b80d2390ac8e2d9ddc955a",
              "size": "32734156"
            }
          ]
        },
        {
          "name": "mkspiffs",
          "version": "0.1.2",
          "systems": [
            {
              "host": "i686-mingw32",
              "url": "https://github.com/igrr/mkspiffs/releases/download/0.1.2/mkspiffs-0.1.2-windows.zip",
              "archiveFileName": "mkspiffs-0.1.2-windows.zip",
              "checksum": "SHA-256:0a29119b8458b61a877408f7995e4944623a712e0d313a2c2f76af9ab55cc9f2",
              "size": "230802"
            },
            {
              "host": "x86_64-apple-darwin",
              "url": "https://github.com/igrr/mkspiffs/releases/download/0.1.2/mkspiffs-0.1.2-osx.tar.gz",
              "archiveFileName": "mkspiffs-0.1.2-osx.tar.gz",
              "checksum": "SHA-256:df656fae21a41c1269ea50cb53752dcaf6a4e1437255f3a9fb50b4025549b58e",
              "size": "115091"
            },
            {
              "host": "i386-apple-darwin",
              "url": "https://github.com/igrr/mkspiffs/releases/download/0.1.2/mkspiffs-0.1.2-osx.tar.gz",
              "archiveFileName": "mkspiffs-0.1.2-osx.tar.gz",
              "checksum": "SHA-256:df656fae21a41c1269ea50cb53752dcaf6a4e1437255f3a9fb50b4025549b58e",
              "size": "115091"
            },
            {
              "host": "x86_64-pc-linux-gnu",
              "url": "https://github.com/igrr/mkspiffs/releases/download/0.1.2/mkspiffs-0.1.2-linux64.tar.gz",
              "archiveFileName": "mkspiffs-0.1.2-linux64.tar.gz",
              "checksum": "SHA-256:1a1dd81b51daf74c382db71b42251757ca4136e8762107e69feaa8617bac315f",
              "size": "46281"
            },
            {
              "host": "i686-pc-linux-gnu",
              "url": "https://github.com/igrr/mkspiffs/releases/download/0.1.2/mkspiffs-0.1.2-linux32.tar.gz",
              "archiveFileName": "mkspiffs-0.1.2-linux32.tar.gz",
              "checksum": "SHA-256:e990d545dfcae308aabaac5fa9e1db734cc2b08167969e7eedac88bd0839667c",
              "size": "45272"
            }
          ]
        }
        ]
      }
   ]
}
```


## Pi Zero version
[hackaday](https://hackaday.io/project/17598-diy-usb-rubber-ducky)
[noaspirit](https://www.novaspirit.com/2016/10/18/raspberry-pi-zero-usb-dongle/)
### Some places to look for scripts 
#### GitHub - note:read licensing before uses
[MTK911](https://github.com/MTK911/Attiny85)
[flashsploit](https://github.com/thewhiteh4t/flashsploit?tab=readme-ov-file)
[DigiDuck-Framework](https://github.com/M4cs/DigiDuck-Framework)
[Duckyspark](https://github.com/toxydose/Duckyspark)
[CedArctic](https://github.com/CedArctic/DigiSpark-Scripts/tree/master)
