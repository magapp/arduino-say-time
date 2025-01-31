# Arduino say time
This little hack uses a VHF/UHF radio to transmit a synthetic voice that tells the time.

## Background
I am often out snowboarding with friends and to communicate we use headsets and UHF radios. This is a really nice way to keep track of each other and also to avoid splitting the group. Besides, when traveling to different countries, mobile phone calling rates may be huge so using radios are very useful. Have a headset with the PTT-button in the glove makes it easy to talk with the group.

One thing that I am missing is to know what time it is. Althought it is not necessary to know the exact time it is nice to at least have a feeling about the clock.

## Say time
This simple Arduino hack is using an Arduino ESP32 Wroom dev board. I am using the Talkie library to produce speech on the high speed PWM output. Once every hour the Talkie library say the current hour on the PWM. Using a low-pass filter the PWM can be connecteced directly into the radio. The power consumption is quite high for the board, but an USB power bank can make it last for a day or more without problem. This could of course be optimized.

![alt tag](/img/example.png)

Simply put the radio and Arduino in your backpack and every hour you'll sync your brain with the current time! The plug is compatble with K-plug (Puxing, Baofeng etc.) and probably others.

## Setup
The program will connect to Wifi and to a NTP-server to ensure the exact time. It will also connect to a MQTT-server where status can be queried and times can be programmed. There are two types of times that can be programmed, "SayTime" and "BeepTime". "SayTime" will transmit the current time when the clock matches and "BeepTime" will transmit only a beep.

LED will light up different brightness of green during wifi setup and different brightness of yellow during MQTT-connection.

In order to setup wifi, ntp-server, mqtt-server, start the ESP32 in configuration mode. This can be achieved by pressing the button during startup. White LED will light up. In configuration mode, the ESP32 will act as an access point so you can connect and do the setup.

## MQTT Commands
To set "SayTime" and "BeepTime" or check status, you can send commands using MQTT. "id" is the board mac adress.

```
say_time/<id>/command  payload: status
say_time/<id>/command  payload: restart
say_time/<id>/command  payload: say
say_time/<id>/command/setTimeOffset  payload nnnn seconds

say_time/<id>/command/setSayTime_1   payload hh:mm
..
say_time/<id>/command/setSayTime_24  payload hh:mm

say_time/<id>/command/setBeepTime_1  payload hh:mm
...
say_time/<id>/command/setBeepTime_24  payload hh:mm
```


## Hardware
The ESP32 used is [AZDelivery Freenove Wroom](https://www.amazon.com/FREENOVE-ESP32-WROOM-Compatible-Wireless-Detailed/dp/B0C9THDPXP) but any dev board can be used.

## Output filter and connections

![alt tag](/img/kplug.png)

```
                      _____
  pin25 >-----||-----|_____|-----+-----> to mic+ input on radio
             100nF     10k       |
                                ---
                                --- 10 nF
                                 |
                                _|_ GND


  pin4 >------------------------------> to PTT input on radio

  gnd  >------------------------------> to gnd on radio
```

