+++ 
draft = false
date = 2021-03-01T13:22:23+02:00
title = "Raspberry Pi 4 4GB Cluster Setup"
slug = "3-raspberry-pi-4-4gb-cluster-setup" 
tags = ["raspberry-pi","kubernetes","cluster","arm64","microk8s"]
categories = ["technology"]
+++

![raspikube](/images/posts/3/raspikube.jpg)

## Setup Raspberry Pis

### Configure PoE fans (automate with ansible)
```bash 
dtoverlay=rpi-poe
dtparam=poe_fan_temp0=57000
dtparam=poe_fan_temp1=60000
dtparam=poe_fan_temp2=63000
dtparam=poe_fan_temp3=66000
```
