---
layout: post
title: 调用linux socket接口设置IP地址
description: ""
category: Code
tags: [Linux, socket]
---

{% include JB/setup %}

linux上设置IP地址经常使用ifconfig命令，比如 ifconfig eth1:0 10.20.2.110 netmask 255.255.255.0 ；而在程序中则可以调用socket接口动态设置IP地址。

程序中可以调用socket接口ioctl函数来设置IP地址，其中参数SIOCSIFADDR命令字设置IP地址、参数SIOCSIFNETMASK名字设置掩码、参数SIOCSIFBRDADDR命令字设置广播地址。示例如下：

变量定义

    int sk;
    struct ifreq req={0};
    struct sockaddr_in* in_addr;
    uint32_t mask, ip;


创建socket，socket类型不限，下面使用UDP类型

    sk = socket(AF_INET, SOCK_DGRAM, IPPROTO_IP);



设置IP地址，sz_if为接口名称，如“eth1:0”；sz_ip为点分十进制IP地址字符串，如“10.20.2.110”

    strncpy(req.ifr_name, sz_if, IFNAMSIZ);
    in_addr = (struct sockaddr_in*)&req.ifr_addr;
    in_addr->sin_family = AF_INET;
    inet_aton(sz_ip, &in_addr->sin_addr);
    ip =  in_addr->sin_addr.s_addr;

    ioctl(sk, SIOCSIFADDR, &req);
    
    
设置掩码，sz_mask为掩码地址字符串，如“255.255.255.0”
    
    strncpy(req.ifr_name, sz_if, IFNAMSIZ);
    in_addr = (struct sockaddr_in*)&req.ifr_netmask;
    in_addr->sin_family = AF_INET;
    inet_aton(sz_mask, &in_addr->sin_addr); 
    mask =  in_addr->sin_addr.s_addr;
     
    ioctl(sk, SIOCSIFNETMASK, &req);

设置广播地址，设置前先根据IP地址和掩码计算出来掩码值 
    
    strncpy(req.ifr_name, sz_if, IFNAMSIZ);
    in_addr = (struct sockaddr_in*)&req.ifr_broadaddr;
    in_addr->sin_family = AF_INET;
    in_addr->sin_addr.s_addr = (ip & mask) | (~mask);
    
    ioctl(sk, SIOCSIFBRDADDR, &req);






