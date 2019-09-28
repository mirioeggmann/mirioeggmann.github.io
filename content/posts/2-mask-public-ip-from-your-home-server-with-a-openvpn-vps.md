+++ 
draft = false
date = 2019-09-28T13:27:10+02:00
title = "Mask public IP from your Home Server with a OpenVPN VPS"
description = ""
slug = "mask-public-ip-from-your-home-server-with-a-openvpn-vps" 
tags = ["openvpn","vps","google-cloud-platform"]
categories = ["technology"]
+++

The first picture shows an example service without a VPS (Virtual Privat Server) in front. It exposes the 
public IP of your home network to the world.

![without_vps](/images/posts/2/without_vps.png)

The second picture shows the example service with a VPS in front. It exposes the IP of your VPS machine fg. an IP
of the Google Cloud Platform. The IP of your home network stays hidden.

![with vps](/images/posts/2/with_vps.png)

## OpenVPN VPS aufsetzen
Go to: Compute Engine -> VM instances -> Create Instance

* Change the machine type to "f1-micro"
* Configure Boot disk
  * OS image: Debian GNU/Linux 9 (stretch)
  * Boot disk type: Standard persistent disk
  * Size (GB): 10

```
Machine A (VPS)
wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh
sudo ufw enable ssh && sudo ufw enable
sudo nano /etc/default/ufw
Change "DEFAULT_INPUT_POLICY" and "DEFAULT_FORWARD_POLICY" to ACCEPT. Save and close the file.
sudo nano /etc/ufw/sysctl.conf
Uncomment n"et/ipv4/ip_forward=1"
sudo nano /etc/ufw/before.rules
At the end of line after the line ""COMMIT" add the following
*nat
-F
:PREROUTING ACCEPT [0:0]
-A PREROUTING -i eth0 -d 193.105.1.1 -p tcp -m multiport --dports 23:65535 -j DNAT --to-destination 10.8.0.2
-A POSTROUTING -s 10.8.0.0/24 ! -d 10.8.0.0/24 -j MASQUERADE
COMMIT


Machine B (Home Server)
sudo apt-get install openvpn
```

<!-- Beginning of tippin.me Button -->
- <div>
<div id="tippin-button" data-dest="mirioeggmann"></div>
<script src="https://tippin.me/buttons/tip.js" type="text/javascript"></script>
</div>
<!-- End of tippin.me Button -->
