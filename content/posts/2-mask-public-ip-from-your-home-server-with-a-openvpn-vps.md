+++ 
draft = true
date = 2019-09-28T13:27:10+02:00
title = "Mask public IP from your Home Server with a OpenVPN VPS"
description = ""
slug = "mask-public-ip-from-your-home-server-with-a-openvpn-vps" 
tags = ["openvpn","vps","google-cloud-platform"]
categories = ["technology"]
+++

![setup](/images/posts/2/setup.png)

The right picture shows an example service without a VPS (Virtual Privat Server) in front. It exposes the 
public IP of your home network to the world.

The left picture shows the example service with a VPS in front. It exposes the IP of your VPS machine fg. an IP
of the Google Cloud Platform. The IP of your home network stays hidden.

## Setup OpenVPN VPS Instance on GCP
Go to: Compute Engine -> VM instances -> Create Instance

* Set your desired name
* Change the machine type to `f1-micro`
* Configure Boot disk
  * OS image: `Debian GNU/Linux 9 (stretch)`
  * Boot disk type: `Standard persistent disk`
  * Size (GB): `10`
* Click `Create`

Go to: VPC Network -> External IP addresses -> RESERVE STATIC IP ADDRESS

* Set your desired name
* Change `Attach to` to your new OpenVPN instance
* IP version: `IPv4`
* Click `Reserve`

## Configure the OpenVPN VPS Instance
Go to: Compute Engine -> VM instances & click on the `SSH` button of your new instance

* Update the machine
```bash
sudo apt update & sudo apt upgrade -y
```
* Install and setup OpenVPN
```bash
wget https://git.io/vpn -O openvpn-install.sh && sudo bash openvpn-install.sh
```
  * For `Public IPv4 address / hostname` enter the static IP address you defined under
  VPC Network -> External IP addresses -> RESERVE STATIC IP ADDRESS
  * Which protocol do you want for OpenVPN connections?: `1) UDP`
  * What port do you want OpenVPN listening to?: `1194`
  * Which DNS do you want to use with the VPN?: `3) Google`
  * Finally, tell me a name for the client certificate.: You can just let it `client`
* Install and enable UFW firewall
```bash
sudo apt install ufw -y
sudo ufw allow ssh && sudo ufw enable
```
* Install and configure fail2ban
```bash
sudo apt install fail2ban -y
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
# you can make changes in the /etc/fail2ban/jail.local file
# after that, restart fail2ban with
sudo service fail2ban restart
```
* Change `DEFAULT_INPUT_POLICY` and `DEFAULT_FORWARD_POLICY` to `ACCEPT`  in `/etc/default/ufw` and then save and close the file.
* Uncomment `net/ipv4/ip_forward=1` in `/etc/ufw/sysctl.conf`
* At the end of line after the line ""COMMIT" add the following in `/etc/ufw/before.rules` (replace
YOUR_ETH0_IP_ADDRESS with the address you get from `ip addr | grep eth0` fg. `10.172.0.2`)
```
*nat
-F
:PREROUTING ACCEPT [0:0]
-A PREROUTING -i eth0 -d YOUR_ETH0_IP_ADDRESS -p tcp -m multiport --dports 23:65535 -j DNAT --to-destination 10.8.0.2
-A POSTROUTING -s 10.8.0.0/24 ! -d 10.8.0.0/24 -j MASQUERADE
COMMIT
```
* Reboot the server
```bash
sudo reboot
```

## Configure the Home Server

* Install OpenVPN
```bash
sudo apt-get install openvpn
```
* Copy the client config from your OpenVPN VPS which is stored under `/root/client.ovpn`
* Connect the home server the the OpenVPN VPS (ATTENTION: after you connect your home server it will only be accessible over the 
VPN... so you have to connect to it over the OpenVPN VPS and from there you can open an SSH session with ssh USERNAME@10.8.0.2)
```bash
sudo openvpn client.ovpn
```

## Further information

New the home server should be accessible over your OpenVPN VPS IP. So if you have any Domains that are 
linked to your Public Home IP you have to change them to your OpenVPN VPS IP.

<div> Feel free to
    <div style="margin-left:5px;margin-right:5px; display:inline-block;" id="tippin-button" data-dest="mirioeggmann"></div> ;)
    <script src="https://tippin.me/buttons/tip.js" type="text/javascript"></script>
</div>
