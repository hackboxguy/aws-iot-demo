# AWS-IoT-Demo
Here is a fun way of experimenting with AWS-IoT on a Commercial-Off-The-Shelf(COTS) hardware running OpenWRT Linux and **aws-iot-device-sdk-cpp-v2** based [aws-iot-pubsub-agent](https://github.com/hackboxguy/aws-iot-pubsub-agent). Custom built linux image for **GL-MT300N-V2** pocket router allows you to configure aws-key/aws-certificate/aws-endpoint/publish-subscribe-topics and custom event-trigger-scripts so that you can see the effect of publish/subscribe messages having physical impact on tangible realworld components like LED-lights/switches as shown in the video below.

## Preparation
1. Download [this](https://github.com/hackboxguy/lfs-downloads/raw/main/gl-mt300nv2-awsiot-demo/gl-mt300nv2-awsiot-demo.bin) openwrt based custom firmware for **GL-MT300N-V2**
1. Overwrite OEM firmware of **GL-MT300N-V2** with custom firmware downloaded in 1st step(ensure that keep-settings is disabled so that we start with default factory settings on next boot)
1. After reboot, webUI of **GL-MT300N-V2** will be available at ip **192.168.20.1** on LAN interface side
1. Userename and password for login page are ```root```/```aws-iot-demo```(later change the pw to your own using openwrt-webUI)
1. Login to your AWS-IoT Core Console, download new device certificate and private-key pair and ensure that your security policy for topic/test/topic* is set correctly for Policy-Action: iot:Publish/iot:Subscribe/iot:Receive/iot:connect(also notedown your endpoint) - Details on how to configure certificates and policies are documented in my [other blog](http://albert-david.blogspot.com/2022/10/re-purpose-your-30-pocket-router-as-aws.html)
1. Upload device-certificate and private-key to your **GL-MT300N-V2** using webUI menu **AWS-IoT-->Upload Device Cert** and **AWS-IoT-->Upload Device Key**
1. Under **AWS-IoT-->Service Settings-->Endpoint** set your aws endpoint(e.g xyz.abc.amazonaws.com)
3. Connect Internet to your **GL-MT300N-V2** device by connect it's WAN port to your home-internet-router or through wifi by specifying the ssid/key under **Network-->Wireless-->radio0-->Scan** setting
1. Reboot **GL-MT300N-V2** using **System-->Reboot-->Perform reboot**
3. After issuing reboot action, wait for about 90seconds(till all LED's on **GL-MT300N-V2** stops blinking), and then toggle the slider switch to see if first LED on **GL-MT300N-V2** follows the slider switch position - this is an indication that your connectivity to aws-iot-core and publish/subscribe are correctly working.

## Demo-Example-1 Preparation
As shown in the video above, to repeate the 1st example, you need to prepare three **GL-MT300N-V2** pocket routers by following steps 1 to 10 as mentioned in **Preparation** section above. In step-6 of Preparation section, ensure that all three **GL-MT300N-V2** routers are uploaded with their own set of certificates and private keys and set three different clientID's as **test-1/test-2/test-3** under **AWS-IoT-->Service Settings-->ClientID**

## Demo-Example-2 Preparation
Demo-Example-2 is same as Demo-Example-1, except **/usr/sbin/led-power.sh** script under **AWS-IoT-->Service Settings-->Subscribe Topic Handler** to be changed to **/usr/sbin/usb-port-power.sh** on all 3 **GL-MT300N-V2** routers
