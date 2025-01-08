IMAGE

# Introduction
I have been on the hunt for an open source smart home hub (not Google or Amazon) and I think I have finally found it in Home Assistant. Of course, this only covers creating the hub for it, but it should be fairly simple to connect the smart appliances to it after.

However, it wasn't as easy as I initially thought it would be to set it up because Home Assistant can be complicated. I did find a very useful guide on how to make a Pi Kiosk here: https://github.com/geerlingguy/pi-kiosk and it is this guide I am using to support me in creating a hub for Home Assistant.
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
sudo apt update
sudo apt upgrade
7. Then run:
curl -sSL https://get.docker.com | sh
8. To add current user to Docker:
sudo usermod -aG docker $USER
9. Log out of your account and then log back in.
10. After logging out, log back in, then verify user was added to Docker by using the following command:
groups
11. To test if Docker is running. run the following command (give it a few moments):
docker run hello-world

## Creating a Static IP for Home Assistant

12. Here, I make use of the guide available at: https://pimylifeup.com/raspberry-pi-static-ip-address/
13. First, run the following command:
ip r | grep default
14. Note the first IP that is mentioned in the result (something like 192.168.4.1 - this is your router IP).
15. Retrieve the current DNS server by using the following command:
sudo nano /etc/resolv.conf
16. Make note of the IP next to nameserver (for me, this was 192.168.0.55 & 1.1.1.1).
17. Exit the nano editor by pressing ctrl + X.
18. Run the following command to bring up network tools:
nmtui
19. Click "Edit a Connection" and select your home connection. Go to IPv4 Configuration and, instead of automatic, make it manual.
20. Click on "Show" to the right of "Manual."
21. For Addresses, add an address that will not get confused for others on your network (192.168.0.88, for example). Your Gateway address is your router IP address and your DNS servers address can be your nameserver IP, or a third party one like Google (8.8.8.8) or Cloudflare (1.1.1.1).
22. Navigate to the bottom and click "OK", then "Back" on the next screen, and "Quit" on the last screen.
23. Now, restart the network manager:
sudo systemctl restart NetworkManager
24. Let's now modify the dhcpcd.conf file with the following command first:
sudo nano /etc/dhcpcd.conf
25. Write the following into that file:
interface <NETWORK>
static ip_address=<STATICIP>/24
static routers=<ROUTERIP>
static domain_name_servers=<DNSIP>
26. For <NETWORK>, replace with "eth0" (Ethernet) or "wlan0" (Wi-Fi). Replace <STATICIP> with the address you put down in part 21. Replace <ROUTERIP> with the router IP from earlier and the <DNSIP> with the nameserver IP (or Google's or Cloudflare's third party options).
27. Save the file with ctrl+X, then Y, then Enter.
28. Reboot your pi:
sudo reboot
29. To test your static IP after rebooting, paste the following in your terminal:
hostname -I
30. You should now be able to see your new static IP address.
## Installing Home Assistant using Docker Compose

31. For this next part of the guide, I relied on: https://pimylifeup.com/home-assistant-docker-compose/
32. First, create a directory for the Home Assistant Docker Compose file:
sudo mkdir -p /opt/stacks/hass
33. Go to the directory:
cd /opt/stacks/hass
34. Let's write a Docker Compose file, first enter the following command to create it and edit it:
sudo nano compose.yaml
35. Copy and paste the following:
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
36. In the above code, we make it so the config files for Home Assistant are kept within the /opt/stacks/hass/hass-config directory. Next, copy the next set of lines right below what was above:
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
37. Replace "<TIMEZONE>" with a valid TZ Timezone value from: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones (for example, living in New York, you'd put "America/New_York."
38. Now, add the next section (replacing the <TIMEZONE> for the same one as you wrote before in the last step and PUID/PGID with yours - to find these, type "id $USER" into terminal):
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
39. And lastly, add:
  hass-configurator:
    image: "causticlab/hass-configurator-docker:latest"
    restart: always
    ports:
      - "3218:3218/tcp"
    volumes:
      - "/opt/stacks/hass/configurator-config:/config"
      - "/opt/stacks/hass/hass-config:/hass-config"
40. Now, to save, press ctrl+X, then Y, then Enter.
41. To enable data persistence and specify the location for mosquitto to log data, we will undertake the following steps.
42. Enter the following into your terminal:
sudo mkdir -p /opt/stacks/hass/mosquitto/config
43. Then:
sudo nano /opt/stacks/hass/mosquitto/config/mosquitto.conf
44. Within this file, copy and paste this:
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
listener 1883
listener 9001
allow_anonymous true
45. Save with ctrl + X, then Y, then Enter.
46. We will now create a user for the mosquitto docker container with UID and GID of 1883. Start off with creating the following:
sudo groupadd -g 1883 mosquitto
47. Then, create user with the following:
sudo useradd -u 1883 -g 1883 mosquitto
48. Then, to take ownership of the mosquitto directory:
sudo chown -R mosquitto: /opt/stacks/hass/mosquitto
49. Node-RED will want to operate under the user with UID 1000, so we must create the directory and take ownership over it:
sudo mkdir /opt/stacks/hass/nodered
51. Then:
sudo chown 1000:1000 /opt/stacks/hass/nodered
52. Now to configure the HASS Configurator:
sudo mkdir /opt/stacks/hass/configurator-config
53. Then:
sudo nano /opt/stacks/hass/configurator-config/settings.conf
54. In here, insert:
{
    "BASEPATH": "../hass-config"
}
55. To save, press ctrl+X, then Y, then Enter.
## The Home Stretch
56. To start up home assistant, enter the following on your command line:
sudo docker compose up -d
57. If you run into an issue ...
