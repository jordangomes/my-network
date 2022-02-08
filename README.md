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
  9. Using the EdgeRouter interface (Services>DHCP) or nmap find the Raspberry pi's IP Address `sudo nmap -O 192.168.1.1/24`
  10. ssh into the Raspberry Pi `ssh {ip} -l {username}`
  11. Run `sudo apt update` and `sudo apt full-upgrade`
  12. Edit `/etc/dhcpcd.conf` and add the following lines to set a static IP address
        ```
        interface eth0
        static ip_address=192.168.1.254
        static routers=192.168.1.1/24
        static domain_name_servers=1.1.1.1
        ```
 13. Reboot the pi and once rebooted try ssh'ing into it with new IP(192.168.1.254)
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
