# ezrouter
Simple dual stack (ipv6/ipv4) router that money can't buy.

## Key features:
* Should work on any hardware that has GNU Linux support
* No external dependencies except a distribution that supports nftables and systemd.
* Dual stack setup (if your internet provider supports it)
* Easy updates through the normal os patching procedures

## The short story
1. Get a computer with two networkcards (or solve it with a vlan switch)
2. Install your favorite GNU Linux version (with systemd support)
3. Follow install instructions
4. done!

## Long story

### what is a router?
A "router" is basically a computer with the following characteristics:

* 2 network cards (lan/wan)
* DHCP-server (nat ips to local network)
* DNS-server (name resolution for nat clients)
* Firewall for network

Other functionalities people are used to:

* Switch (to connect more devices by cable)
* WIFI network

### Suggested hardware:
Generic suggestions

    * Switch with VLAN support (802.11q)
    * Stand alone wifi node
    * Computer

My current setup is: 

    * Ubiquiti EdgeSwitch 5XP
    * Ubiquiti Unifi AC Lite
    * Asus Tinkerboard

### Preparations

Install your chosen os on the computer. 

Configure Switch for vlan:

    Port              1    2    3    4
    Management (1)    E    U    U    U
    Wan (2)           U    T    T    E
    * E = Exclude
    * U = Untagged
    * T = Tagged

Switch connections:

    Port 1 Internet from provider
    Port 2 Router computer
    Port 3 Spare port for any device
    Port 4 Wifi Node
    
### Setup

Prerequirements:
Install nftables and systemd-resolved. You should also remove all other network configuration and firewall once setup is complete.

This example assumes lan is named **end0**, WAN is **vlan 2** in the switch and the source files are in **/opt/ezrouter**.
    
    # copy firewall file
    cp /opt/ezrouter/nftables* /etc/
    
    # copy network files
    cp -r /opt/ezrouter/network/* /etc/systemd/network/
    
    # edit files
    Edit files to your liking (check that if names are correct etc)
    
    # enable systemd-resolved to act as a local network dns server
    echo "DNSStubListenerExtra=192.168.0.1" >> /etc/systemd/resolved.conf
    
    # enable services to start at boot
    systemctl enable nftables systemd-networkd systemd-resolved
    
    # stop all other network services
    # Note: depends on what you distribution ran before as network management
    systemctl disable NetworkManager
    
    # reboot
    # done!
