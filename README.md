# AWS-IoT-Demo
Here is a fun way of experimenting with AWS-IoT on a Commercial-Off-The-Shelf(COTS) hardware running OpenWRT Linux and [aws-iot-device-sdk-cpp-v2](https://github.com/aws/aws-iot-device-sdk-cpp-v2) based [aws-iot-pubsub-agent](https://github.com/hackboxguy/aws-iot-pubsub-agent). Custom built linux image for **GL-MT300N-V2** pocket router allows you to configure aws-key/aws-certificate/aws-endpoint/publish-subscribe-topics and custom event-trigger-scripts so that you can see the effect of publish/subscribe messages having physical impact on tangible realworld components like LED-lights/switches as shown in the video below.

[![Fun with AWS-IoT](http://img.youtube.com/vi/1vdC4lBXq0s/0.jpg)](http://www.youtube.com/watch?v=1vdC4lBXq0s)

## Setup diagram for overwriting OEM firmware of GL-MT300N-V2 router

![Setup Diagram.](/images/setup-diagram.png "Setup Diagram.")

## Preparation
1. Download [this](https://github.com/hackboxguy/lfs-downloads/raw/main/gl-mt300nv2-awsiot-demo/gl-mt300nv2-awsiot-demo.bin) openwrt based custom firmware for **GL-MT300N-V2**
1. As shown in the picture above, connet your browser-pc to LAN side network of the pocket-router and power it ON, with OEM firmware, webUI of the pocket router is accessible at ip **192.168.8.1**(note: after overwriting OEM firmware, webUI will be available at ip **192.168.20.1**).
1. Overwrite OEM firmware of **GL-MT300N-V2** with custom firmware downloaded in 1st step(ensure that keep-settings is disabled so that we start with default factory settings on next boot)
1. After reboot, webUI of **GL-MT300N-V2** will be available at ip **192.168.20.1** on LAN interface side
1. Userename and password for login page are ```root```/```aws-iot-demo```(later change the pw to your own using openwrt-webUI)
1. Connect Internet to your **GL-MT300N-V2** device by connecting it's WAN port to your home-internet-router or through wifi by specifying the ssid/key under **Network-->Wireless-->radio0-->Scan** setting
1. Login to your AWS-IoT Core Console, download new device certificate and private-key pair and ensure that your security policy for topic/test/topic* is set correctly for Policy-Action: iot:Publish/iot:Subscribe/iot:Receive/iot:connect(also notedown your endpoint) - Details on how to configure certificates and policies are documented in my [other blog](http://albert-david.blogspot.com/2022/10/re-purpose-your-30-pocket-router-as-aws.html)
1. Upload device-certificate and private-key to your **GL-MT300N-V2** using webUI menu **AWS-IoT-->Upload Device Cert** and **AWS-IoT-->Upload Device Key** (note:Thoug clicking submit button uploads the Device-Cert/Device-Key files **correctly**, but there is no success message given to the user as feedback - I plan to fix this in the future by showing a success/fail message)
1. Under **AWS-IoT-->Service Settings-->Endpoint** set your aws endpoint(e.g xyz.abc.amazonaws.com) and click on **Save & Apply** button.
1. To see if the connection to AWS-IoT-Core is successful, Open **AWS-IoT-->Service Log** and check the last line of the log shows something like - **[INFO] [2024-03-17T10:57:38Z] [77ad3ce0] [socket] - id=0x77ad9650 fd=11: connection success**.
1. Toggle the slider switch of the pocket-router and see if first LED on this router follows the state of slider switch position - this is an indication that your connectivity to AWS-IoT-Core and publish/subscribe are correctly working.

## Demo-Example-1 Preparation and Configuration
![Demo-Example-1 Diagram.](/images/demo-example-1.png "Demo-Example-1 Diagram.")
As shown in the picture above, to repeate the 1st example, you need to prepare three **GL-MT300N-V2** pocket routers by following steps 1 to 10 as mentioned in **Preparation** section above. In step-8 of Preparation section, ensure that all three **GL-MT300N-V2** routers are uploaded with their own set of certificates and private keys and set three different clientID's as **test-1/test-2/test-3** under **AWS-IoT-->Service Settings-->ClientID** (Note: In this picture, Internet to the pocket routers are provided through mobile-data-network using sim-card, but you could simplify this setup by connecting WAN port of all 3 routers to your home-internet-router or configure all 3 routers to act as a wifi clients to your home-wifi)

## Demo-Example-2 Preparation and Configuration
![Demo-Example-2 Diagram.](/images/demo-example-2.png "Demo-Example-2 Diagram.")
Demo-Example-2 is same as Demo-Example-1, except **/usr/sbin/led-power.sh** script under **AWS-IoT-->Service Settings-->Subscribe Topic Handler** to be changed to **/usr/sbin/usb-port-power.sh** on all 3 **GL-MT300N-V2** routers (This example uses, USB powered LED's similar to the one found [here](https://www.amazon.de/OSALADI-LED-Lampe-USB-Laptop-Laptop-Tastatur-Nachtlicht/dp/B08MJD4P17)

## Demo-Example-3 Preparation and Configuration
![Demo-Example-3 Diagram.](/images/demo-example-3.png "Demo-Example-3 Diagram.")
For this demo you will need a [volume-control-knob](https://www.amazon.de/-/en/VAYDEER-USB-Control-Adjuster-Compatible/dp/B08V4ZB5MV) and [blink(1)](https://blink1.thingm.com/) USB based RGB-LED dongle. As shown in the picture above, 1st-pocket-router is attached with a volume-control-knob as event publisher and remaining two routers are configured as subscribers and they change the color of the usb-led based on published button event from 1st router. For this demo, following are the settings of all 3 units set through web-interface.

#### Settings of 1st unit(publisher)
1. AWS-IoT-->Event Settings-->Key Event MUTE Topic : **test/topic_led**
2. AWS-IoT-->Event Settings-->Key Event MUTE Message : **{"powerstate": "toggle"}**
3. AWS-IoT-->Event Settings-->Key Event PREV-SONG Topic : **test/topic_led**
4. AWS-IoT-->Event Settings-->Key Event PREV-SONG Message : **{"ledstate": "red"}**
5. AWS-IoT-->Event Settings-->Key Event NEXT-SONG Topic : **test/topic_led**
6. AWS-IoT-->Event Settings-->Key Event NEXT-SONG Message : **{"ledstate": "green"}**
7. AWS-IoT-->Event Settings-->Key Event PLAY-PAUSE Topic : **test/topic_led**
8. AWS-IoT-->Event Settings-->Key Event PLAY-PAUSE Message : **{"ledstate": "blue"}**

#### Settings of 2nd and 3rd units(Subscribers)
1. AWS-IoT-->Service Settings-->Subscribe Topic : **test/topic_led**
2. AWS-IoT-->Service Settings-->Subscribe Topic Handler : **/usr/sbin/usb-status-led.sh**

## Demo-Example-4 Preparation and Configuration
![Demo-Example-4 Diagram.](/images/demo-example-4.png "Demo-Example-4 Diagram.")
This demo-example is not shown in the video, but as shown in the picture above, it uses 1st-pocket-router to publish the temperature read from [usb-temp-sensor-dongle](https://www.amazon.de/Docooler-PCsensor-Thermometer-Datenlogger-Recorder/dp/B07BFBSF57) and the 2nd-pocket-router displays the temperature on [digispark-attiny85-usb-to-i2c-display](https://github.com/hackboxguy/brbox/tree/master/sources/firmware/attiny85-i2c-tiny-usb) and the 3rd-pocket-router will operate the [usb-relay](https://www.amazon.de/-/en/ARCELI-LCUS-1-Module-Intelligent-Control/dp/B09MD8MBKC) based on its temperature-threshold and hysterisis setting.

#### Settings of 1st unit(publisher) - every 5 second it reads temp from usb-dongle and publishes to test/topic_temperature using usb-temper-read.sh script, -1 count indicates loop forever
1. AWS-IoT-->Service Settings-->Publish Topic : **test/topic_temperature**
2. AWS-IoT-->Service Settings-->Publish Interval Sec : **5**
3. AWS-IoT-->Service Settings-->Publish Count : **-1**
4. AWS-IoT-->Service Settings-->Publish Message : **/usr/sbin/usb-temper-read.sh**

#### Settings of 2nd unit(subscriber) - displays received temperature value on to i2c based oled display(ssd1306 128x32)
1. AWS-IoT-->Service Settings--> Subscribe Topic : **test/topic_temperature**
2. AWS-IoT-->Service Settings--> Subscribe Topic Handler : **/usr/sbin/display-data.sh**

#### Settings of 3rd unit(subscriber) - compares the published temperature and controls the relay state based on set threshold and hysterisis
1. AWS-IoT-->Service Settings--> Subscribe Topic : **test/topic_temperature**
2. AWS-IoT-->Service Settings--> Subscribe Topic Handler : **/usr/sbin/temperature-relay-control.sh**
3. AWS-IoT-->Service Settings--> Subscribe Threshold : **40**
4. AWS-IoT-->Service Settings--> Subscribe Hysterisis : **5**

On 1st unit, when body temperature of usb-temper-sensor crosses 40degrees, Relay on 3rd unit turns ON and when temperature cools down to 35(or below), Relay will turn OFF
