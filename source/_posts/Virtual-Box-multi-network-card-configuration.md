---
title: Virtual Box multi-network card configuration
date: 2018-05-21 17:57:58
categories:
- Virtual Machine
tags:
- Experiment
- Configuration
---

# Setup Virtual Machine 

I use the virtual box to contribute the enviroment to do some experiment for distributed system like do some hadoop and spark , rabbitmq redis cluster ...

# Set the network bridge 

The all machine will work in virtual isolation and then will simulatie a phycial machine to use then contribute the many cluster enviroment.

But the network configuration is an importent thing to do ,we need config a network to do the IPC on network so we need a bridge to handle the all data transform .

It's simple to do .

<!--more-->

![BRIDGE CONFIG](http://ww1.sinaimg.cn/mw690/0079qc5ply1frj4df0jj6j30uk0kitb7.jpg)

click the right button which is a plus sign on the icon then we can create a new bridge to use .

# Set the configuration in centos

I assume that you had setuped the centos or other linux edition .

## Virtual Machine config network card 

open the settings for a virtual machine and select the setting button

![OPEN CARD](http://ww1.sinaimg.cn/mw690/0079qc5ply1frj4ghmmvij31680vudsg.jpg)

when we choose the network setting we can add a network card to add a new card and then we should generate a new mac address for use 

![ADD CARD](http://ww1.sinaimg.cn/mw690/0079qc5ply1frj4fyzgz4j311c0r2gpe.jpg)

## In CentOS to config

Because I choose the CentOS 7 minimum edition so the network is not open after the setup,we need open the network service .

![STEP ONE](http://ww1.sinaimg.cn/mw690/0079qc5ply1frj4i8plw5j30o00ff0wh.jpg)

Under the directory /etc/sysconfig/network-scripts/ is the all network config and which contain the **ifcfg-** prefix file is the network card config .

we can see the first is the ensp03 is the first card for us to work in the NAT pattern .
And then set some configuration .
* ONBOOT=yes
* BOOTPROTO=dhcp
Use the dhcp pattern we can get a ip address automatic . 

Another we should set it work in the static pattern , it's that this card will work use the static ip address and then we set by ourselves .

![STEP TWO](http://ww1.sinaimg.cn/mw690/0079qc5ply1frj4nviphpj30o10fgju2.jpg)

As we can see we should set the **DEVICE** to define the card name . and then we can use ifconfig or ip addr to find if we set them success .

![STEP THREE](http://ww1.sinaimg.cn/mw690/0079qc5ply1frj4qet39ej30o10bytgu.jpg)

And the last thing we just use the command service network start or systmctl start network.service

