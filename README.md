# PiWall - Raspberry Pi Intrusion Detection System & a honeypot

PiWall stands as a virtual guardian for our home network.

The project utilizes various open source software
packages, installed on Raspberry Pi 4 for
network monitoring and analysis. For Instance, we are using
Snort to configure the IDS where Snort is the sensor module
that provides raw traffic analysis and packet monitoring.

Then we are using Cowrie which is a python based, medium interaction honeypot hich can
very efficiently record the entire shell interaction during an
attack, along with being able to log brute force attempts.

We are also setting up a DHCP & DNS server on our pi with the help of Dnsmasq, its is an excellent lightweight utility that provides network infrastructure for small networks





## Prerequisites

List the hardware and software prerequisites for your project. For example:
- Raspberry Pi 4
- Raspberry Pi OS
- An SD card
- A micro-usb power cable
- An Ethernet cable


## Raspberry Pi installation 

https://www.raspberrypi.com/software/

## Adding new user

Its no good idea to do everything as root, if this account gets compromized, we've got some serious problems. To avoid this we will now create a user who will not be root, but who will be allowed to run commands as root when needed.

## DHCP & DNS server

``` 
sudo apt install dnsmasq 
  ```

Dnsmasq provides DHCP with address reservation.This just means the DHCP server will assign us an IP, but it will be the same IP every time. Second, it doubles as a DNS server. Therefore, we will be able to access our devices by name. No more having to remember IPs!

The default configuration file for dnsmasq is /etc/dnsmasq.conf.However, we are going to add our specific configuration to a new file in /etc/dnsmasq.d. so that there are no problems with new default config files when we update dnsmasq through the package manager.

**PLEASE DISABLE DHCP IN YOUR ROUTER BEFORE PROCEEDING**

Now lets restart dnsmasq with new config

```
sudo service dnsmasq restart
```
### Adding machines to your network

Each time we want to put a new machine on your network you will need to get its MAC address and add a dhcp-host entry in your config file. We can do this by a variety of means. On Linux, ifconfig will list your MAC as HWaddr. Similarly, on Windows, ipconfig /all will list MAC addresses.

There is a trick, however, that we used a lot when adding machines to our network. Before we bring our new device online, SSH into dnsmasq machine and run the following:

```
tail -f /var/log/syslog | grep DHCP
```

This will show us log information from dnsmasq. Next, start up your new device. When it requests an address, dnsmasq will log "No address available" along with the MAC of the requesting device. Copy that MAC into your dhcp-host entry for the device and restart dnsmasq

![mac addr](https://github.com/HelixY2J/PiWall/blob/main/img/mac_addr.png)

## Cowrie

Cowrie enables an
attacker to initiate a connection and login to the system,
fooling them to believe that they are entering the legit system
SSH session. Once the brute-force attack to crack the password is successful, the attacker is redirected to the decoy
operating system with which they can interact. Once the
attacker enters the decoy system, the system mimics the actual
system and can monitor and log all the malicious actions
performed by the attacker.



## Snort 

Snort rule is constructed to generate an alert
whenever an external IP address (IPs from outside home
network) attempts to initiate an ICMP Ping.