- [COMMANDS PACKET TRACER](#commands-packet-tracer)
  - [Basic configs](#basic-configs)
  - [Router basic config](#router-basic-config)
  - [Switch basic config](#switch-basic-config)
  - [PC basic config](#pc-basic-config)
  - [Telnet remote access](#telnet-remote-access)
  - [Show configs](#show-configs)
  - [SSH remote access](#ssh-remote-access)
  - [DHCP](#dhcp)
  - [RIP protocol - dynamic routes](#rip-protocol---dynamic-routes)



# COMMANDS PACKET TRACER

## Basic configs
- `enable`: enable privileged EXEC mode
- `#conf t`: config mode
- `(config)hostname "router1"`: change name to "router1"
- `(config)banner motd #banner message#`: make "banner message" be the message of the day
- `(config)enable password "passw"`: not encrypted password
- `(config)enable secret "passw"`: encrypted password
- `(config)line console 0`: router/switch initial config 
- `(config-line)password "passw"`: add entry password
- `(config-line)login`: enables login
- `(config-line)logging synchronous`: prevent system log messages from interrupting command entry
- `(config-line)exec-timeout 15`: add timeout
- `(config)interface fastEthernet "0/0"`: enter interface "0/0"
- `(config-if)description "rede local de coimbra"`: add description
- `(config-if)ip address <ip> <mask>`: add IP address and mask




## Router basic config
- open interface;
- turn power off;
- add portas;
- turn power on;
- go to CLI.

CLI:
- `enable`
- `conf t`: enter configuration mode
- `interface fastEthernet "0/0"`: enter interface "0/0"
- `ip address <ip> <mask>`: add IP address and mask
- `no shut`: enable management interface
- `exit`



## Switch basic config
- open interface;
- turn power off;
- add portas;
- turn power on;
- go to CLI.

CLI:
- `enable`
- `conf t`: enter configuration mode
- `interface vlan "1"`: enter vlan "1"
- `ip address <ip> <mask>`: add IP address and mask
- `no shut`: enable management interface
- `exit`



## PC basic config
- Desktop tab;
- IP configuration: add IP, mask, gateway;
- command prompt: `ping <gateway>`



## Telnet remote access
- `(config)line vty 0 4`: remote access to lines 0 to 4
- `(config-line)logging synchronous`: prevent system log messages from interrupting command entry
- `(config-line)exec-timeout 15`: add timeout
- `(config-line)password "passw"`: add entry password
- `(config-line)login`: enables login with the password assigned
- `(config)interface vlan "1"`: enter vlan "1"
- `(config)ip default-gateway <ip>`: insert gateway (router IP)
- `(config-if)ip address <ip> <mask>`: add IP address and mask
- `(config-if)no shut`: enable management interface



## Show configs
- `#show running-config`: show configs in use
- `#show startup-config`: show configs in nvram
- `#show ip interface`: show interface configs
- `#show ip interface brief`: show stats of interface configs in each router
- `#show etherchannel summary`: show ethernet configs
- `#show spanning-tree`: show spanning-tree configs (STP)



## SSH remote access
- `(config)hostname "hostname"`: give hostname to router/switch
- `(config)ip domain-name "domain.name"`: create domain
- `(config)crypto key generate rsa`: generate key for ssh access
- `(config)username "username" [privilege 15] secret "password"`: create username with password and full access [privilege 15]
- `(config)line vty 0 1`: enter vty config mode
- `(config)login local`: creates local authentication
- `(config)transport input ssh`: ensures remote access to be SSH only
- `ssh -l "username" "ip"`: access client side



## DHCP
- `(config)ip dhcp pool "name"`: creates DHCP pool
- `(dhcp-config)network "192.168.1.0" "255.255.255.0"`: gives IP and mask to the DHCP pool
- `(dhcp-config)default-router "192.168.1.1"`: sets default gateway for DHCP clients
- `(dhcp-config)dns-server "192.168.1.10"`: sets DNS for DHCP clients
- `(config)ip dhcp excluded-address "192.168.1.1" "192.168.1.10"`: excludes a range of IP addresses from being assigned to the DHCP pool 
- `(config)ip helper-address "IP of DHCP"`: configures IP for DHCP requests



## RIP protocol - dynamic routes
- `router rip`
- `version 2`
- `network "ip"`
- `network "ip"`
- `network "ip"`