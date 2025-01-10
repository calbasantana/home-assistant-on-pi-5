![Image_1](https://github.com/user-attachments/assets/635fd991-1529-4a37-a733-58e3cb115cbc)
# Introduction
I have been on the hunt for an open source smart home hub (not Google or Amazon) and I think I have finally found it in Home Assistant. Of course, this only covers creating the hub for it, as going much more beyond than is something that will be highly specific to each household.

However, it wasn't as easy as I initially thought it would be to set it up because Home Assistant can be complicated. I was thankful there were many guides available, but had to kind of get different things from different guides and this resulted in a single project here, which combines all of them into one.
# Home Assistant on Pi 5 Components
The following components were purchased for this project:
*  Vilros Raspberry Pi 5 Starter Kit MAX - Turbo Cooled Aluminum - (128GB Edition) - (8GB RAM) - purchased for $159.99 from: https://www.amazon.com/Vilros-Raspberry-Starter-Kit-MAX/dp/B0D232HN74/ref=sr_1_3?crid=2P7BOXQATJ26I&dib=eyJ2IjoiMSJ9.DOPSBKzDUbr_p92FPgJ0S7I_GQPuGx-2kEdYNtHX82YzEjwVX19U-FxrTyjFf1GMW7mS4XxAme2mS66pBtONwl5Oc9ZgW23N4Hj285ZBUSeGGdipj7VSn1tFj_t2PQ9Ja_jUEBykKs7ygOFvwzgFqG8eWdY0KfsUhYxljiroOwv83UOKlBB1gBEdImtrWIIx1w0KhTzjrDdKtNTDalcWRgt6sWqVlWaWTJycrEaoXlU.vulbTsX8tRrYN_3VAII8HSdWJk_5l0PEi7KnP2Bf54M&dib_tag=se&keywords=pi%2B5%2Bvilros%2Bstarter%2Bkit%2Bmax&qid=1736187449&sprefix=pi%2B5%2Bvilros%2Bstarter%2Bkit%2Bmax%2Caps%2C80&sr=8-3&th=1
*  Touch Screen Monitor with Case,ROADOM 10.1’’ Raspberry Pi Screen, IPS FHD 1024×600,Responsive and Smooth Touch,Dual Built-in Speakers,HDMI Input,Compatible with Raspberry Pi 5/4/3/Zero,Versatile Stand - purchased for $109.99 from: https://www.amazon.com/ROADOM-Raspberry-Responsive-Compatible-Versatile/dp/B0CJNKFVPY/ref=sr_1_3?crid=2SRF5ERJ1CJTO&dib=eyJ2IjoiMSJ9.tA9seA5wtEabMknYU1mHFodQQg2xb94wTEWYXwQqADLkt7dXNL441rgxp2q_h98t3_GNSlkCBu62zW5RBcMPGGi3v6Rewzl1MKM-3ocZyU3pEh5RGBjlwzwnjbhxFkj3q8dyG-2Qdh5cINVe8042PK9Z5dU-nZXJEw5pZTsOiu5VjesmZ95eJpELK1SR76e-4jxnIWWXJ7ijRYyradEvZRknHlv6TmuFZ28rVrQcmDc.OIv0AGUk3SvvRqOPKlHnNE_upohw3y2y5S2btIH41y8&dib_tag=se&keywords=roadom%2Btouch%2Bscreen%2Bmonitor%2B10.1&qid=1736187524&sprefix=roadom%2Btouch%2Bscreen%2Bmonitor%2B10.1%2Caps%2C79&sr=8-3&th=1

The total cost of the project is approximately ~$270. excluding taxes.
# Initial Assembly
While the official screen guide recommends to provide power to the screen through the Pi, I advise against it since it results in undervolting. Instead, have the official charging block for the Pi powering the Pi and the USB-C block from the screen powering the screen. The final connections should look like this:

![Image_2](https://github.com/user-attachments/assets/c35ca69e-7c96-4a50-8189-207ffe0ec893)


# Software Installation
Installing Home Assistant is, as mentioned before, the most difficult part, but let's get to it.

## Installing Pi OS
1. Place the microSD card from the Pi 5 kit inside a microSD card reader and connect it to your computer.
2. Flash the default Pi OS using Pi Imager onto the microSD card and insert the SD card into your Pi. It will be tricky to get in there, but it's doable:

![Image_3](https://github.com/user-attachments/assets/a43f5660-72b6-49ff-a0b3-ca69b5b5ee23)

3. Once you boot up the Pi and screen, this is what you should see:

![Image_4](https://github.com/user-attachments/assets/b0e3ce37-abb5-4c0d-baf7-c55581e134c5)

4. Go through the installation procedure for Pi OS and then we will install Docker.

## Docker Installation

5. To install Docker, I will be following this guide from: https://pimylifeup.com/raspberry-pi-docker/
6. First, use:
```bash
sudo apt update
```
```bash
sudo apt upgrade
```
7. Then run:
```bash
curl -sSL https://get.docker.com | sh
```
8. To add current user to Docker:
```bash
sudo usermod -aG docker $USER
```
9. Log out of your account and then log back in.
10. After logging out, log back in, then verify user was added to Docker by using the following command:
```bash
groups
```
11. To test if Docker is running. run the following command (give it a few moments):
```bash
docker run hello-world
```
## Creating a Static IP for Home Assistant

12. Here, I make use of the guide available at: https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address-on-raspbian-raspberry-pi-os/74428#74428
13. We will create a static IP through DHCP. First, run the following command:
```bash
ip -4 addr show | grep global
```
14. Note the first IP that is mentioned in the result (something like 192.168.4.185 - this is your Pi's IP and the one we will use to make a static IP). Also note the number that follows (/22 or /24; this is the network size and important later).
15. Then, run the following command:
```bash
ip route | grep default | awk '{print $3}'
```
16.The result will be your router IP. Note this down as well (should look like 192.168.4.1).
17. Finally, find your DNS server address with the following command:
```bash
cat /etc/resolv.conf
```
18. For me, this was 192.168.0.55 & 1.1.1.1; it's OK to have multiple, but I just wrote down the first one.
19. We will create our static IP by doing the following:
```bash
sudo nano /etc/dhcpcd.conf
```
20. Here, insert the following (if using WiFi):
```bash
interface wlan0
static ip_address=192.168.4.185/22
static routers=192.168.4.1
static domain_name_servers=192.168.0.55
```
21. Of course, insert your IPs instead of mine and remember the /22 is the network size, so if yours is different, insert a different value.
22. Exit and save the nano editor by pressing ctrl + X, Y, then Enter.
23. Now, restart the network manager:
```bash
sudo systemctl restart NetworkManager
```
24. Then reboot your pi:
```bash
sudo reboot
```
25. To test your static IP after rebooting, paste the following in your terminal:
```bash
hostname -I
```
26. You should now be able to see your new static IP address. Just to doublecheck, though, make sure you can access the Internet through a browser.

## Installing Home Assistant using Docker Compose

27. For this next part of the guide, I relied on: https://pimylifeup.com/home-assistant-docker-compose/
28. First, create a directory for the Home Assistant Docker Compose file:
```bash
sudo mkdir -p /opt/stacks/hass
```
29. Go to the directory:
```bash
cd /opt/stacks/hass
```
30. Let's write a Docker Compose file, first enter the following command to create it and edit it:
```bash
sudo nano compose.yaml
```
31. Copy and paste the following:
```bash
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:stable"
    volumes:
      - /opt/stacks/hass/hass-config:/config
      - /etc/localtime:/etc/localtime:ro
    restart: unless-stopped
    privileged: true
    network_mode: host
```
32. In the above code, we make it so the config files for Home Assistant are kept within the /opt/stacks/hass/hass-config directory. Next, copy the next set of lines right below what was above:
```bash
  nodered:
    container_name: nodered
    image: nodered/node-red
    ports:
      - 1880:1880
    volumes:
      - /opt/stacks/hass/nodered:/data
    depends_on:
      - homeassistant
      - mosquitto
    environment:
      - TZ=<TIMEZONE>
    restart: unless-stopped
```
33. Replace "<TIMEZONE>" with a valid TZ Timezone value from: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones (for example, living in New York, you'd put "America/New_York."
34. Now, add the next section (replacing the <TIMEZONE> for the same one as you wrote before in the last step and PUID/PGID with yours - to find these, type "id $USER" into terminal):
```bash
  mosquitto:
    image: eclipse-mosquitto
    container_name: mosquitto
    restart: unless-stopped
    ports:
      - 1883:1883
      - 9001:9001
    volumes:
      - "/opt/stacks/hass/mosquitto/config:/mosquitto/config"
      - "/opt/stacks/hass/mosquitto/data:/mosquitto/data"
      - "/opt/stacks/hass/mosquitto/log:/mosquitto/log"
    environment:
      - TZ=<TIMEZONE>
    user: "${PUID}:${PGID}"
```
35. And lastly, add:
 ```bash
  hass-configurator:
    image: "causticlab/hass-configurator-docker:latest"
    restart: always
    ports:
      - "3218:3218/tcp"
    volumes:
      - "/opt/stacks/hass/configurator-config:/config"
      - "/opt/stacks/hass/hass-config:/hass-config"
```
36. Now, to save, press ctrl+X, then Y, then Enter.
37. To enable data persistence and specify the location for mosquitto to log data, we will undertake the following steps.
38. Enter the following into your terminal:
```bash
sudo mkdir -p /opt/stacks/hass/mosquitto/config
```
39. Then:
```bash
sudo nano /opt/stacks/hass/mosquitto/config/mosquitto.conf
```
40. Within this file, copy and paste this:
```bash
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
listener 1883
listener 9001
allow_anonymous true
```
41. Save with ctrl + X, then Y, then Enter.
42. We will now create a user for the mosquitto docker container with UID and GID of 1883. Start off with creating the following:
```bash
sudo groupadd -g 1883 mosquitto
```
43. Then, create user with the following:
```bash
sudo useradd -u 1883 -g 1883 mosquitto
```
44. Then, to take ownership of the mosquitto directory:
```bash
sudo chown -R mosquitto: /opt/stacks/hass/mosquitto
```
45. Node-RED will want to operate under the user with UID 1000, so we must create the directory and take ownership over it:
```bash
sudo mkdir /opt/stacks/hass/nodered
```
46. Then:
```bash
sudo chown 1000:1000 /opt/stacks/hass/nodered
```
47. Now to configure the HASS Configurator:
```bash
sudo mkdir /opt/stacks/hass/configurator-config
```
48. Then:
```bash
sudo nano /opt/stacks/hass/configurator-config/settings.conf
```
49. In here, insert:
```bash
{
    "BASEPATH": "../hass-config"
}
```
50. To save, press ctrl+X, then Y, then Enter.

## The Home Stretch

51. To start up home assistant, enter the following on your command line:
```bash
sudo docker compose up -d
```
52. You will get something like this; just give it a few minutes:

![Image_5](https://github.com/user-attachments/assets/23fc653f-9003-4b1b-b01b-44537d5d276c)

## Setting Up Home Assistant
53. Now, go to a browser page and insert the following (using your static IP instead of <IPADDRESS>:
```bash
http://<IPADDRESS>:8123
```
54. You will be greeted with the following:

![Image_6](https://github.com/user-attachments/assets/f53fcece-7df2-4e9b-a295-e925cdc55c6d)

55. Sign up or log in and then let's set up the Dashboard once you're ready.
56. To create your Dashboard, go to "Settings" on the bottom left (you may need to scroll). Then click on Dashboards.
57. On the next screen, click "Add Dashboard" at the bottom right.
58. You will have a list of different options for your Dashboard. I recommend clicking on "Webpage."
59. You will now have an option of URL, which, for us can be one of two of the following:

Node-RED:
```bash
http://<IPADDRESS>:1880
```
Configurator:
```bash
http://<IPADDRESS>:3218
```
60. Personally, I went with Node-RED.
61. For Title, I recommend Node-RED if you went with that or Configurator if you went with that instead. Then choose whichever Icon you like. Do toggle the Admin setting to ON so it hides this element if the user is not an admin.
62. You should now see Node-RED on the sidebar to the left. We must now configure Node-RED and MQTT.
63. Click on your Profile at the bottom left corner, then the second "Security" tab. Scroll down until you see "Long-lived access tokens" and click "Create Token." Name it Node-RED.
64. You will now have a very long string of letters, numbers, and other stuff; this token will allow Node-RED to communicate with the server. Copy it to your clipboard.
65. Click on Node-RED from the sidebar to the left.
66. Now, with Node-RED open, click on the three horizontal lines to the right, where my cursor is below:

![Image_7](https://github.com/user-attachments/assets/218cf34c-57dc-4a29-8cfe-043092980e95)

67. Click on "Manage palette" and then on the "Install" tab. In the searchbar, search for:
```bash
node-red-contrib-home-assistant-websocket
```
68. Press "Install" and then "Install" in the next popup.
69. Close out of the interface and then search where it says "filter nodes" for events:all.

![Image_8](https://github.com/user-attachments/assets/0b2164b1-d749-4157-9575-6b18dd565b1c)

70. Drag "events:all" to Flow 1. Then doubleclick the node to configure it.

![Image_9](https://github.com/user-attachments/assets/4f0b3606-ada9-4993-ad28-e6bc5c597874)


71. Click the icon right of the pencil. Here, we will add Home Assistant Docker as a contactable server.

![Image_10](https://github.com/user-attachments/assets/6d5c6b05-8aff-4c01-bb66-6b84d95528a2)

72. Set the Base URL as
```bash
http://<IPADDRESS>:8123
```
73. For example, mine would be http://192.168.4.185:8123
74. Paste the access token from your clipboard using Ctrl + v. Then, click "Add" and close the next interface. Click "Deploy" and you should get that it was successfully deployed.
75. We will now add MQTT to Home Assistant. Click on Settings on the sidebar to the left, then "Devices & services."
76. Click "Add Integration" on the bottom right. Search for MQTT and then click on MQTT. You will be given another list of options. Click on the top one (just "MQTT").
77. For broker, set it as the local address of your Home Assistant (for me, this would be 192.168.4.185) and click "Submit."
78. You should now be all set! From here, you can customize your Dashboard further using Node-RED guides.
