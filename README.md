# Network Setup
## EdgeRouter Setup 
 1. Disconnect all cables from EdgeRouter except for computer which needs to be connected to eth0
 2. Reset EdgeRouter by holding in the pinhole on the back
 3. Set a static ip address on computer
    | IP Address  | Subnet Mask   |
    | ----------- | ------------- |
    | 192.168.1.2 | 255.255.255.0 |
 4. Navigate to https://192.168.1.1/ in browser
 5. login with username and password ubnt
 6. Navigate to the Wizards tab an choose the "WAN+2LAN2" Wizard
 7. Under DNS forwarding set the DNS servers to 1.1.1.1 and 1.0.0.1
 8. Under user setup update the username and password(make sure to record details in password manager)
 9. Apply the config and while the EdgeRouter is rebooting connect the WAN cable to eth0 and move the computer to eth1
 10. Set the ethernet interface on the computer back to DHCP
 11. Navigate to https://192.168.1.1/ in browser again and login with the credentials chosen earlier
 12. Open the bottom menu and click the System Tab and update the Timezone 
 13. In a new tab navigate to http://www.ubnt.com/download and download the latest firmware (EdgeRouter X SFP)
 14. Back in the router interface upload the latest firmware and wait for the update to complete (confirm reboot if necessary)
 15. Navigate to https://192.168.1.1/ in browser again and login
 16. Enable POE for the port connected to the UniFi Access Point

## Raspberry Pi Setup (Docker and Portainer)
 ### Pi Setup
  1. Download the Raspberry Pi Imager from - https://www.raspberrypi.com/software/
  2. Download the latest lite image from - https://www.raspberrypi.com/software/operating-systems/
  3. Connect SD card to computer and open the Raspberry Pi Imager
  4. Click "CHOOSE OS" and "Use custom" then select the image downloaded in step 2
  5. Click "CHOOSE STORAGE" then select the SD Card
  6. Finally select the settings cog and tick "Enable SSH" and update the username and password as well as the locale settings
  7. Exit the advanced settings, Click "WRITE" then wait for the image to be written
  8. Once the image has been written plug the SD Card into the PI and connect power and ethernet
  9. Using the EdgeRouter interface (Services>DHCP) or nmap find the Raspberry Pi's IP Address `sudo nmap -O 192.168.1.1/24`
  10. ssh into the Raspberry Pi `ssh {ip} -l {username}`
  11. Run `sudo apt update` and `sudo apt full-upgrade`
  12. Edit `/etc/dhcpcd.conf` and add the following lines to set a static IP address
        ```
        interface eth0
        static ip_address=192.168.1.254
        static routers=192.168.1.1/24
        static domain_name_servers=1.1.1.1
        ```
  13. Reboot the Pi and once rebooted try ssh'ing into it with new IP(192.168.1.254)
 ### Docker/Portainer Setup 
  1. Run the following commands and wait for docker to install
     ```
     curl -fsSL https://get.docker.com -o get-docker.sh
     sudo sh get-docker.sh
     ```
  2. To create a volume for portainer - 
     ```
     sudo docker volume create portainer_data
     ```
  3. Run the following command to install portainer
     ```
     sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data cr.portainer.io/portainer/portainer-ce:latest
     ```
  4. Navigate to https://192.168.1.254:9443/ and complete the setup(make sure to record login details in password manager)

## Pi Hole Setup
 1. In Portainer fold down "App Templates" and click "Custom Templates"
 2. Add a custom template called "pihole", add a description and paste the contents of [pihole-compose.yml](docker-compose/pihole-compose.yml) then update the WEBPASSWORD variable and create the temlate
 3. Click on the "pihole" template you just created and click "Deploy the stack"
 4. Login to EdgeRouter and go to Services>DHCP and delete the DHCP service
 5. Verify you can log into the pihole interface from https://192.168.1.254/admin

## UniFi Controller Setup
 1. In Portainer fold down "App Templates" and click "Custom Templates"
 2. Add a custom template called "unifi-controller", add a description and paste the contents of [unifi-controller-compose.yml](docker-compose/unifi-controller-compose.yml) then create the temlate
 3. Click on the "unifi-controller" template you just created and click "Deploy the stack"
 5. Navigate to https://192.168.1.254:8443 and dismiss the SSL error
 6. Follow the prompts to setup the network (make sure to tick "Combine 2 GHz and 5 GHz WiFi Network Names into one")
 7. Once the setup is complete go to Settings>System>Application Configuration and turn on "Override Inform Host" and set the Host for inform to "192.168.1.254"
 8. Reset the Unifi access point using the pinhole on the bottom
