+++ 
draft = false
date = 2019-09-25T19:09:56+02:00
title = "Setup Docker Buildx on Debian"
description = "The post explains how to setup buildx."
slug = "setup-docker-buildx-on-debian"
tags = ["docker","buildx"]
categories = ["docker"]
series = ["docker"]
+++

- Install docker-ce according to [Get Docker Engine - Community for Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
- Add your user to the `docker` group and remember to log out and back in for this to take effect!
```
 sudo usermod -aG docker $USER
```
- When you now type in `docker ps` without `sudo` it should then return
```
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
- Create a `.docker` folder in your `$HOME`
```
mkdir ~/.docker
```
- Add the following to `~/.docker/config.json`
```
{
 "experimental": "enabled"
 }
```
- `docker buildx ls` should now return
```
$ docker buildx ls
NAME/NODE DRIVER/ENDPOINT STATUS  PLATFORMS
default * docker                  
  default default         running linux/amd64, linux/386
```
- Setup a new buildx builder
```
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
docker buildx create --name testbuilder --driver docker-container --use
docker buildx inspect --bootstrap
```
- The result of `docker buildx inspect --bootstrap` should be
```
$ docker buildx inspect --bootstrap
[+] Building 4.3s (1/1) FINISHED                                                                                                                                                        
 => [internal] booting buildkit                                                                                                                                                    4.3s
 => => pulling image moby/buildkit:buildx-stable-1                                                                                                                                 3.6s
 => => creating container buildx_buildkit_testbuilder0                                                                                                                             0.7s
Name:   testbuilder
Driver: docker-container

Nodes:
Name:      testbuilder0
Endpoint:  unix:///var/run/docker.sock
Status:    running
Platforms: linux/amd64, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/arm/v7, linux/arm/v6
```
- You are now ready to use Docker Buildx!
