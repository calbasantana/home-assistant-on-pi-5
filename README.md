IMAGE

# Introduction
I have been on the hunt for an open source smart home hub (not Google or Amazon) and I think I have finally found it in Home Assistant. Of course, this only covers creating the hub for it, but it should be fairly simple to connect the smart appliances to it after.
# Home Assistant on Pi 5 Components
The following components were purchased for this project:
*  Vilros Raspberry Pi 5 Starter Kit MAX - Turbo Cooled Aluminum - (128GB Edition) - (8GB RAM) - purchased for $159.99 from: https://www.amazon.com/Vilros-Raspberry-Starter-Kit-MAX/dp/B0D232HN74/ref=sr_1_3?crid=2P7BOXQATJ26I&dib=eyJ2IjoiMSJ9.DOPSBKzDUbr_p92FPgJ0S7I_GQPuGx-2kEdYNtHX82YzEjwVX19U-FxrTyjFf1GMW7mS4XxAme2mS66pBtONwl5Oc9ZgW23N4Hj285ZBUSeGGdipj7VSn1tFj_t2PQ9Ja_jUEBykKs7ygOFvwzgFqG8eWdY0KfsUhYxljiroOwv83UOKlBB1gBEdImtrWIIx1w0KhTzjrDdKtNTDalcWRgt6sWqVlWaWTJycrEaoXlU.vulbTsX8tRrYN_3VAII8HSdWJk_5l0PEi7KnP2Bf54M&dib_tag=se&keywords=pi%2B5%2Bvilros%2Bstarter%2Bkit%2Bmax&qid=1736187449&sprefix=pi%2B5%2Bvilros%2Bstarter%2Bkit%2Bmax%2Caps%2C80&sr=8-3&th=1
*  Touch Screen Monitor with Case,ROADOM 10.1’’ Raspberry Pi Screen, IPS FHD 1024×600,Responsive and Smooth Touch,Dual Built-in Speakers,HDMI Input,Compatible with Raspberry Pi 5/4/3/Zero,Versatile Stand - purchased for $109.99 from: https://www.amazon.com/ROADOM-Raspberry-Responsive-Compatible-Versatile/dp/B0CJNKFVPY/ref=sr_1_3?crid=2SRF5ERJ1CJTO&dib=eyJ2IjoiMSJ9.tA9seA5wtEabMknYU1mHFodQQg2xb94wTEWYXwQqADLkt7dXNL441rgxp2q_h98t3_GNSlkCBu62zW5RBcMPGGi3v6Rewzl1MKM-3ocZyU3pEh5RGBjlwzwnjbhxFkj3q8dyG-2Qdh5cINVe8042PK9Z5dU-nZXJEw5pZTsOiu5VjesmZ95eJpELK1SR76e-4jxnIWWXJ7ijRYyradEvZRknHlv6TmuFZ28rVrQcmDc.OIv0AGUk3SvvRqOPKlHnNE_upohw3y2y5S2btIH41y8&dib_tag=se&keywords=roadom%2Btouch%2Bscreen%2Bmonitor%2B10.1&qid=1736187524&sprefix=roadom%2Btouch%2Bscreen%2Bmonitor%2B10.1%2Caps%2C79&sr=8-3&th=1

The total cost of the project is approximately ~$270. excluding taxes.
# Initial Assembly
This part is pretty straightforward. mount the Pi 5 on the backside of the monitor and make the appropriate connections.
# Software Installation
Installing Home Assistant is super simple, they even have a guide here for Raspberry Pi systems: https://www.home-assistant.io/installation/raspberrypi/

1. Place the microSD card from the Pi 5 kit inside a microSD card reader and connect it to your computer.
2. Download the Home Assistant image for Pi 5 from: https://github.com/home-assistant/operating-system/releases/download/14.1/haos_rpi5-64-14.1.img.xz
3. Flash the image using Pi Imager to the microSD card.
4. Once done, put the microSD card into the Pi 5 and boot it up. You should see this:

IMAGE

5. Follow the instructions for Onboarding from: https://www.home-assistant.io/getting-started/onboarding/
6. Once done, you should see the default dashboard:

IMAGE

7. You should be good to go and now have something to easily control your smart devices from!
# Tips
None at the moment! This is a straightforward guide with a lot of documentation available online already.
