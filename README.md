# NextcloudInDockerOnRaspberry2
A Nextcloud server on running via a docker image on a raspberry 2.

We get the latest Hypriot Image from https://blog.hypriot.com/downloads/. We flash out SD card with

$ sudo dd if=<HYPRIOT_IMAGE> of=/dev/<SDCARD_IDENTIFIER> bs=4M status=progress && sync

Once the Raspberry is connected to the local network and started with the flashed SD card inserted, we can find it's IP address in the network via

$ sudo nmap -sP 192.168.178.30/24 | awk '/^Nmap/{ip=$NF}/B8:27:EB/{print ip}'

192.168.1.* will be your local network mask.
The default login for Hypriot is user:pirate,pw:hypriot. Now that we can login from the within the network via

$ ssh pirate@<RASPBERRY_IP>

We can check if everything works well by

$ docker info

which should give some sensible output.
It's always a good idea to change the default login password

$ passwd

Then we update the packages 

$ sudo apt update && sudo apt dist-upgrade -y

Now we install docker compose for easier management of our containers

$ sudo apt install docker-compose

Now we get the official nextcloud container for our raspberry

$ docker pull ownyourbits/nextcloudpi-armhf

Finally we run our nextcloud container

$ docker run -d -p 4443:4443 -p 443:443 -p 80:80 -v ncdata:/data --name nextcloudpi ownyourbits/nextcloudpi-armhf <RASPBERRY_IP>

This gives us a running nextcloud container from an image named nextcloudpi with a mounted volume named ncdata that stores the nextcloud data at /data in the host container.


We can start this container next time simply by

$ docker start nextcloudpi &

With

$ docker logs -f nextcloudpi


We can follow the progress of the initialization. We are done when Init done is printed. Then we can enter the Raspberry Pi's IP into our browser. Because the SSL certificate is self signed we need to allow an exception in our browser. Then we can follow the Nextcloud setup.





Sources:
https://blog.hypriot.com/getting-started-with-docker-and-windows-on-the-raspberry-pi/
https://raspberrypi.stackexchange.com/questions/13936/find-raspberry-pi-address-on-local-network
https://ownyourbits.com/2017/11/15/nextcloudpi-dockers-for-x86-and-arm/
