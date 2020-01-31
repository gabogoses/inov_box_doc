<p align="center">
  <a href="https://inovshop.com">
    <img alt="Inovshop" src="https://www.inovshop.com/wp-content/uploads/2017/12/img_logo_color.png" width="27%" />
  </a>
</p>
<h1 align="center">
  Invovbox v2
</h1>

<h3 align="center">
  ⚛️ 📄 🚀
</h3>
<h3 align="center">
  January 2020
</h3>
<p align="center">
Inovbox is a digital signage player. It is based on Raspberry Pi and on other mini-pc solution. Inovbox can be centrally managed by Inovhub
</p>


## What’s In This Document

- [Get Up and Running](#-get-up-and-running)
- [Structure of the App](#-structure-of-the-app)
- [You Must Know](#-you-must-now)
- [Process](#process)
- [Connect Through SSH](#-connect-through-ssh)
- [Copy Files and Directories](#-copy-files-and-directories-between-your-local-file-and-a-box-via-scp)
- [Create ISO From Inovbox](#-create-iso-from-inovbox)
- [Duplicate Inovbox with ISO and SD Card](#-duplicate-inovbox-with-iso-and-sd-card)
- [Troubleshoot](#troubleshoot)


## The App is Built on the Following Stack

- [Node.js](https://nodejs.org/en/) All the logic used Node.js that allows server-side execution of JavaScript code.
- [Next.js](https://nextjs.org/) React.js frameword used to connect the box to a wifi access
- [Python](https://www.python.org/) & [Pygame](https://www.pygame.org/wiki/GettingStarted) are used to manage the setup screen (showing mac address, ssid, password)


## 🚀 Get Up and Running

1. **Raspberry requirements**

- [Raspbian](https://www.raspberrypi.org/downloads/raspbian/)
- [Node.js](https://nodejs.org/en/) v.8.0 or newer
- [Python](https://www.python.org/) v3.0 or newer
- [PM2](https://pm2.keymetrics.io/)
- [OMXPlayer](https://www.raspberrypi.org/documentation/raspbian/applications/omxplayer.md)
- [hostapd](https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md)
- [DNSMasq](https://wiki.debian.org/dnsmasq)
- [scrot](https://www.unixmen.com/scrot-a-command-line-screenshot-tool/)

2. **Getting start**
	***Quick start***

   ```shell
   # Download the newest version in /opt/ of the raspbian folder
   sudo git clone git@bitbucket:supertec-alpha/inovbox-v2.git
   
   # Install dependencies
   sudo npm install
   
   # Run the app
    sudo npm run dev
   ```

	***Automatic start***
	 ```shell
   # Download the newest version in /opt/ of the raspbian folder
   sudo git clone git@bitbucket:supertec-alpha/inovbox-v2.git
   
   # Install dependencies
   sudo npm install
   
   # Edit the bash_profile file and add these few lines
    cd /opt/inovbox_v2
    sudo pm2 start server.js
   ```


Reboot the box thanks to  ```sudo reboot``` and Inovbox V2 is now running.
At this point, you’ve got a fully functional Inovbox app.

## ⚙️ Directory Structure

```
+-- assets/ 
+-- data/
|   +-- images/
|   +-- samples/ #default settings
|		+-- config.json
|		+-- sequence.json
|		+-- settings.json
|	+-- config.json
|	+-- sequence.json
|	+--	settings.json
+-- modules/
|	+-- wifi/
|		+-- wifi.js
|	+-- config.js
|	+-- downloadManager.js
|	+-- files.js
|	+-- sequence.js
|	+-- settings.js
+-- pages/
+-- main.py
+-- package.json
+-- server.js
```


| FILE / DIRECTORY      | Description                                                                 | 
| --------------------- | ------------------------------------------- |
| assets/               | all media files will be found in this folder|       
| images/               | splashscreens (loading, register, wifi)     |
| sample/               | default settings files folder               |
| config.json           | config file updated by the server, to locate on which server the box will be connected
| sequence.json         | list all details about media files, used to launch the playlist with OMXPlayer
| settings.json         | contain settings about rotation, debug, button
| wifi.js               | wifi and hotspot mode settings              |
| config.js             | create config.json and read it              |
| downloadManager.js    | managing files downloading                  |
| pages/                | contain app (next.js) to connect the box to a wifi access (url: 10.10.10.10)
| main.py               | manage the setup screen (showing mac address, ssid, password)
| package.json          | List of dependencies                         |
| server.js             | endpoint of the app                         |

## 💡 You Must Know

- **Memory-split needs to be set at 256MB of RAM [for a good use](https://www.raspberrypi.org/documentation/configuration/config-txt/memory.md) .**
 The easiest way to change it is via raspi-config:
 ```shell
sudo raspi-config
```
For the change to take effect, the box need to be restarted
 ```shell
sudo reboot
```

- [**OMXPlayer**](https://www.raspberrypi.org/documentation/raspbian/applications/omxplayer.md) : is a video player only available for Raspberry Pii and [**Mplayer**](http://www.mplayerhq.hu/DOCS/HTML/en/index.html) is a video player used for other boxes (rpi).

- [**hostapd**](https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md):  is a Linux package required to set up the box as a wireless access point and enable the hotspot mode.

- [**DNSMasq**](https://wiki.debian.org/dnsmasq): is a Linux package required to set up a DNS server on a box.

- [**scrot**](https://www.unixmen.com/scrot-a-command-line-screenshot-tool/): is a Linux package required to takes screenshot.

### MAC Address
To get the MAC address of a box, you need to read the file ```/sys/class/net/your-card-name/address```

Example: 
 ```shell
sudo cat /sys/class/net/eth0/address
```
**eth0**: be careful, the name of your card can be different, make sure you have the good name by typing in your terminal the following command: ```ifconfig``` 
You will need it to generating the SSID to configure the hotspot mode in the file located at ```/inovbox_v2/module/wifi/wifi.js```

If the name of your card is different you need to update the functions ```generateSSID()``` and ```getMacAddress()``` in ```/inovbox_v2/modules/wifi/wifi.js``` and change **eth0** with the name of your card.

### Reboot
Inovbox reboot automatically every 8 hours. You can change the setting inside ```.bash_profile```

## 🎛️Process

### Initialization :
	- Read mac address
	- Read sequence
	- Read config file
	- wifi test connection
### After Initialization :
	- Generate SSID and password for hotspot mode
		CASE 1 - The box is connected to internet:
				case 1 - The box has a sequence and connects to the server
				=> The box will download the files of the playlist from inovhub. When the files are downloaded it will start playing
				case 2 - The box does not have sequence and it will show the MAC Address
		CASE 2 - The box isn't connected to internet:
				case 1 - The box has a sequence and it will play it
				case 2 - The box do not have sequence and it will show the hotspot screen to connect it to internet


## 🔑 Connect Through SSH
Make sure your box is properly set up and connected.
You will need to note down the IP address of your box using the  ```ifconfig``` command. It will display information about the current network status including the IP address.

If the SSH server is unavailable it can be enabled it with ```systemctl```
```shell
sudo systemctl enable ssh
sudo systemctl start ssh
```

```shell
ssh username@ip-address
```

The box will require a password which is by default the user password.

## ✂️ Copy Files and Directories Between your Local File and a Box via SCP
To copy a file from a local to a remote system run the following command:
```shell
scp file.txt remote_username@box-ip-address:/remote/directory
```
To copy a directory from a local to a remote system run the following command:
```shell
scp -r /local/directory remote_username@box-ip-address:/remote/directory
```
You will be asked to enter the user password.

##  📦 Create ISO From Inovbox
1. Open terminal
2. For Linux type ```lsblk``` to know your disk name. For mac type ```diskutil list```
3. Use the following command ```sudo dd bs=1024 if=path/of/your/disk of=/path/to/save.img```
example : ```sudo dd bs=1024 if=/dev/sdb/ of=/home/user/document/inovbox.iso```
 
##  💾 Duplicate Inovbox with ISO and SD Card
1. Get the complete Inovbox ISO image.
2. Burn the image using [Balena Etcher](https://www.balena.io/etcher/)
3. Insert the programmed SD card into Raspberry Pi and power on
[More info on image installation](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)
 
## ❗Troubleshoot

You'll find here some examples of knowing issues and how to resolve it:

### ➡️ Black screen & frozen screen
- no sequences assigned from inovhub platform
- the sequence is assigned but there is no media inside,
- check if the media brand is assigned to the store which the Inovbox is linked
### ➡️ Server connection failed
- check the internet connection using a ping ```ping www.google.com```
