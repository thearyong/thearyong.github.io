---
title: 蓝牙BLE开发小结
date: 2018-05-22 22:47:56
tags:
---

## 蓝牙

### 概述
**蓝牙**（英语：Bluetooth）。这是一种[无线通讯](https://zh.wikipedia.org/wiki/%E7%84%A1%E7%B7%9A%E9%80%9A%E8%A8%8A)技术标准，用来让固定与移动设备，在短距离间交换数据，以形成[个人局域网](https://zh.wikipedia.org/wiki/%E5%80%8B%E4%BA%BA%E5%8D%80%E5%9F%9F%E7%B6%B2%E7%B5%A1)（PAN）。其使用短波[特高频](https://zh.wikipedia.org/wiki/%E7%89%B9%E9%AB%98%E9%A0%BB)（UHF）[无线电波](https://zh.wikipedia.org/wiki/%E7%84%A1%E7%B7%9A%E9%9B%BB%E6%B3%A2)，经由2.4至2.485 GHz的[ISM频段](https://zh.wikipedia.org/wiki/ISM%E9%A0%BB%E6%AE%B5)来进行通信[[1\]](https://zh.wikipedia.org/wiki/%E8%97%8D%E7%89%99#cite_note-1)。1994年由电信商[爱立信](https://zh.wikipedia.org/wiki/%E6%84%9B%E7%AB%8B%E4%BF%A1)（Ericsson）发展出这个技术[[2\]](https://zh.wikipedia.org/wiki/%E8%97%8D%E7%89%99#cite_note-2)。它最初的设计，是希望创建一个[RS-232](https://zh.wikipedia.org/wiki/RS-232)数据线的无线通信替代版本。它能够链接多个设备，克服同步的问题。

### 版本
2010年7月，蓝牙技术联盟推出了蓝牙4.0规范。其最重要的特性是支持省电。提出了“低功耗蓝牙”、“传统蓝牙”和“高速蓝牙”三种模式。

- 高速蓝牙主攻数据交换与传输
- 传统蓝牙则以信息沟通、设备连接为重点
- 低功耗蓝牙以不需占用太多带宽的设备连接为主

2016年6月，[**蓝牙技术联盟**](https://www.bluetooth.com/zh-cn)推出第五代蓝牙5.0。在有效传输距离上将是4.2LE版本的4倍（理论上可达300米），传输速度将是4.2LE版本的2倍（速度上限为24Mbps）。

| 技术规范                                                     | 典型蓝牙                                                     | [低耗电蓝牙](https://zh.wikipedia.org/wiki/%E4%BD%8E%E8%80%97%E9%9B%BB%E8%97%8D%E7%89%99) |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [无线电](https://zh.wikipedia.org/wiki/%E7%84%A1%E7%B7%9A%E9%9B%BB)频率 | 2.4 GHz                                                      | 2.4 GHz                                                      |
| 距离                                                         | 10米/100米                                                   | 30米                                                         |
| 空中数据速率                                                 | 1-3 Mb/s                                                     | 1 Mb/s                                                       |
| 应用吞吐量                                                   | 0.7-2.1 Mb/s                                                 | 0.2 Mb/s                                                     |
| 安全                                                         | 64/128-bit及用户自定义的应用层                               | 128-bit [AES](https://zh.wikipedia.org/wiki/%E9%AB%98%E7%BA%A7%E5%8A%A0%E5%AF%86%E6%A0%87%E5%87%86)及用户自定义的应用层 |
| [强健性](https://zh.wikipedia.org/w/index.php?title=%E5%BC%B7%E5%81%A5%E6%80%A7_(%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)&action=edit&redlink=1) | [自动适应快速跳频](https://zh.wikipedia.org/w/index.php?title=%E8%87%AA%E5%8B%95%E9%81%A9%E6%87%89%E5%BF%AB%E9%80%9F%E8%B7%B3%E9%A0%BB&action=edit&redlink=1)，[FEC](https://zh.wikipedia.org/wiki/FEC)，快速[ACK](https://zh.wikipedia.org/wiki/ACK) | 自动适应快速跳频                                             |
| 发送数据的总时间                                             | 100 ms                                                       | <6 ms                                                        |
| [网络拓扑](https://zh.wikipedia.org/wiki/%E7%B6%B2%E7%B5%A1%E6%8B%93%E6%92%B2) | [分散网](https://zh.wikipedia.org/w/index.php?title=%E5%88%86%E6%95%A3%E7%B6%B2&action=edit&redlink=1) | [星状拓扑](https://zh.wikipedia.org/wiki/%E6%98%9F%E5%9E%8B%E7%BD%91)（Star） [总线拓扑](https://zh.wikipedia.org/wiki/%E5%8C%AF%E6%B5%81%E6%8E%92%E6%8B%93%E6%92%B2)（Bus） [网状拓扑](https://zh.wikipedia.org/wiki/%E7%B6%B2%E7%8B%80%E6%8B%93%E6%92%B2)（Mesh） |
| 耗电量                                                       | 1（作为引用）                                                | 0.01至0.5（视使用情况）                                      |
| 最大操作电流                                                 | <30 mA                                                       | <15 mA（最高运行时为15 mA）                                  |
| 主要用途                                                     | [手机](https://zh.wikipedia.org/wiki/%E6%89%8B%E6%A9%9F)，[游戏机](https://zh.wikipedia.org/wiki/%E9%81%8A%E6%88%B2%E6%A9%9F)，[耳机](https://zh.wikipedia.org/wiki/%E8%80%B3%E6%A9%9F)，立体声音频流， [汽车](https://zh.wikipedia.org/wiki/%E6%B1%BD%E8%BB%8A)和[PC](https://zh.wikipedia.org/wiki/PC)等 | 手机，游戏机，PC，[表](https://zh.wikipedia.org/wiki/%E8%A1%A8)，[体育](https://zh.wikipedia.org/wiki/%E9%AB%94%E8%82%B2)和[健身](https://zh.wikipedia.org/wiki/%E5%81%A5%E8%BA%AB)，[医疗保健](https://zh.wikipedia.org/wiki/%E9%86%AB%E7%99%82%E4%BF%9D%E5%81%A5)， 汽车，[家用电子](https://zh.wikipedia.org/w/index.php?title=%E5%AE%B6%E7%94%A8%E9%9B%BB%E5%AD%90&action=edit&redlink=1)，[自动化](https://zh.wikipedia.org/wiki/%E8%87%AA%E5%8B%95%E5%8C%96)和[工业](https://zh.wikipedia.org/wiki/%E5%B7%A5%E6%A5%AD)等 |

### 协议
蓝牙协议堆栈依照其功能可分四层：
- 核心协议层（HCI、LMP、L2CAP、SDP）
- 线缆替换协议层（RFCOMM）
- 电话控制协议层（TCS-BIN）
- 选用协议层（PPP、TCP、IP、UDP、OBEX、IrMC、WAP、WAE）

![img](https://images2015.cnblogs.com/blog/506370/201603/506370-20160308162412288-1389526862.png)

上面哔哔那么多，主要为了蓝牙的不同版本区别，及其不同场景的应用。接下来记录的主要是现阶段工作中，BLE 开发的一些小结，不深，抛砖引玉。如有不对，欢迎斧正！



## BLE 

### 简介

BLE，全称 **Bluetooth Low Energy**，蓝牙4.0推出的一个低功耗模式。

1. **BLE设备分单模和双模：**

    - 单模简称BLE或者LE,商标为Bluetooth Smart，只能和 BLE 设备通信。
    - 双模简称BR，Bluetooth Smart Ready。兼容传统蓝牙，可以和传统蓝牙通信，也可以和BLE通信，常用在手机上，android4.3和IOS4.0之后版本都支持BR，也就是双模设备。单模只能和BR和单模的设备通信，不能和传统蓝牙通信，由于功耗低，待机长，所以常用在手环的智能设备上。


2. **BLE分为中心设备和外围设备：**
    - 中心设备：用来连接其他外围设备，例如手机等。
    - 外围设备：是非常小或者简单的低功耗设备，用来提供数据，被中心设备连接，例如小米手环，耳机。

    大部分情况下，外设通过广播自己来让中心设备发现自己，并建立 GATT 连接，从而进行更多的数据交换。这里有且仅有两个角色，发起连接的一方，叫做中心设备—Central，被连接的设备，叫做外设—Peripheral。

    ![24](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.024.jpeg)

### BLE 协议栈 


![05](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.005.jpeg)

#### ATT/GATT

![img](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.008.jpeg)
再形象点：

![img](https://upload-images.jianshu.io/upload_images/8413594-4cd27b410b7f5a5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

1. Profile
2. service
3. characteristic
4. Descriptor

#### UUID

![09](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.009.jpeg)

#### GAP

![10](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.010.jpeg)





### BLE 广播

![18](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.018.jpeg)




### BLE 连接流程

![img](https://upload-images.jianshu.io/upload_images/8413594-18a9d1769a765386.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/626)



1. 扫描
2. 连接
3. 读写、订阅、通知
   - 订阅：indicate，一定会收到数据

   - 通知：notify，有可能会丢失数据
4. 设置最大传输单元MTU
5. 获取设备的实时信号强度Rssi



### BLE数据传输率

1. BLE Data Rate受到多种复杂因素的影响，通过修改一些BLE连接参数是有可能提高BLE Data Rate的。 
2. 影响BLE Data Rate的因素主要有 ：

- 对方BLE设备的Data Rate限制，最终能达到的最大Data Rate取决于两个设备中最慢的那个设备。 
- BLE连接参数，包括connection interval, slave latency, connection supervision timeout, packets per connection interval，通过修改这些参数可以提高Data Rate。 
- MTU(Maximum Transfer Unit)的大小，增加MTU可以减少包头对data rate的开销，但是有些设备的蓝牙协议栈不支持修改MTU。 
- BLE芯片方案的速度限制，MCU的运算能力会影响数据吞吐量。 
- 某些手机操作系统的限制，比如iOS对BLE的连接参数有限制，在iOS上达不到spec规定的最大理论data rate；各家Android手机厂商对BLE的连接参数也有修改，不同Android手机的最大BLE Data Rate有不一样。 
-  其他无线信号的干扰，比如WiFi和Bluetooth工作在同频段，会对Bluetooth造成干扰，增大误码率，提升重传次数，降低Data Rate。 
3. BLE Data Rate的提升会带来功耗的增加，因此在不进行大块数据传输时，应该将BLE连接参数恢复到平衡模式或者节能模式。 
4. 从Android 5.0开始增加了两个API来调整BLE连接参数和MTU: BluetoothGatt::requestConnectionPriority(int connectionPriority)和BluetoothGatt::requestMtu(int mtu)。需要注意的是BluetoothGattServer是不支持这两个API，原因是core spece有规定，只能有Gatt Client发起request。 
5. 最新的Bluetooth 5.0将BLE data rate提升了一倍(double)，但是支持Bluetooth 5.0的设备最快要2017年才有。

以下是两篇关于BLE Data Rate的论述 ：
- 这是一篇简短的英文论述，可以很快给你想要的答案：[BLE连接参数对iOS和Android上Data Rate的影响](https://atmosphere.anaren.com/wiki/Data_rates_using_BLE)
- 这一篇汇总和深入探讨BLE Data Rate的繁体中文论述（需要翻墙），包含了几篇我看过的英文材料的内容（所以我才不打算自己写深入的研究结果了）：[BLE Data Rate专题讨论](http://thinkingiot.blogspot.fi/2015/12/ble-data-rate-1.html)



## BLE 扩展


### 穿戴设备 

- 耳机
- 手环、手表
- 眼镜

### 健康医疗

- 血压仪、血糖仪等
- 温度测量

### 智能家居

- 智能锁
- 灯
- 空调、冰箱、窗帘等

### 室内定位 

![img](https://pic1.zhimg.com/80/v2-2abd096c51c1c60a3c69bd66444da357_hd.jpg)

#### beacon

![17](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.017.jpeg)

[android-beacon-library](https://github.com/AltBeacon/android-beacon-library)



### Mesh组网 







## Q&A

### BLE 避坑及优化

#### 1. 扫描不到蓝牙设备
- 检查是否在主线程里调用 scan，部分设备（如三星手机）需要在主线程调用 scan、connect。
- 连接设备前，先关闭扫描蓝牙。否则连接成功后，再次扫描会发生阻塞，扫描不到设备。
- Android M（android 6.0 动态权限）中必须拥有定位权限才能扫描BLE设备。
- android 8.0须动态申请权限。

#### 2. 连接不上蓝牙设备

- 检查设备是否被占用
- android 部分设备需要申请权限，不然也会出现扫描得到，但是连接会失败

#### 3. 蓝牙 mac 地址变化

- android 6.0之后，对于使用 WLAN API 和 Bluetooth API 的应用，Android 移除了对设备本地硬件标识符的编程访问权。

   - 本机调用`WifiInfo.getMacAddress()` 方法和 `BluetoothAdapter.getAddress()` 方法，现在会返回常量值 `02:00:00:00:00:00`。

   - 当发起后台 WLAN 或蓝牙扫描时，在外部设备看来，该操作的发起来源是一个随机化 MAC 地址。

#### 4. 属性RESPONSE 和 NO_RESPONSE区别

#### 5. ble 服务端放置一段时间后，客户端发现不了服务端从设备

#### 6.  连接成功后马上断开连接

- 这个与BLE设备有关，建议在硬件端进行优化，尝试调整设备的连接参数（ConnectionInterval、SlaveLatency、 SupervisionTimeout），优化设置BLE连接过程中的传输速度和功耗。尤其是 SupervisionTimeout这个超时时间，如果BLE在这个时间内没有发生通信的话，就会自动断开。这些参数APP端无法修改。

#### 7. 优化
- 断开后如果需要重连，也请延迟一段时间，否则会造成阻塞。
-  两次操作之间最好间隔一小段时间，如100ms（具体时间可以根据自己实际蓝牙外设自行尝试延长或缩短）。举例，onConnectSuccess之后，延迟100ms再进行notify，之后再延迟100ms进行write。

​    

   **原则：**

   ![34](http://7rf9ir.com1.z0.glb.clouddn.com/Android-BLE-in-Action.034.jpeg) 




### 捍地售后排查

1. 手机捍地扫描不到机器人
  - 手机是否打开蓝牙，或者禁止了app 蓝牙权限
  - 系统设置-蓝牙，看是否能扫描到捍地机器人
2. 手机捍地连接不上机器人(区别第一个问题，可以扫描到，但连接不上)

  



