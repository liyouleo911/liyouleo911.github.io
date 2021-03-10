---
layout: post
title:  "N1 恢复官改固件教程"
categories: N1 
---

# 准备材料：
1. USB_Burning_Tool_v2.1.6.8
   
2. W大救砖包（T1_1.3T47_mod_by_webpad_v3_20180419_2.img）+ 密钥文件（SECURE_BOOT_SET）
   
3. W大官改固件（N1_mod_by_webpad_v2.2_20180920.img）
   
4. 好用的USB双头线

# 普通刷机教程：
1. 先把USB双公头线连接到电脑USB口与N1的第二个口（靠HDMI口），N1不要通电
   
2. 打开USB Burning Tool，导入W大官改固件，验证通过后，出现开始字样
   
3. 勾选擦出FLASH，不要勾选擦除bootloader，USB Burning Tool点击开始运行刷机，3秒钟内速度让N1通电
   
4. USB Burning Tool开始正常识别N1线刷模式，刷机开始
   
5. 烧录完成后，拔电重启，N1恢复了原来的样子，可以正常ADB连接，进入线刷，重新安装其它固件

# 短接刷机教程：
1. 安装USB Burning Tool软件，安装好WorldCup Device驱动(设备管理器->操作->添加过时硬件），打开USB Burning Tool软件安装位置，新建license文件夹，放入密钥文件

2. 断开N1的DC供电和USB接口，两个都要拔掉

3. USB双公头线连接电脑USB口和N1靠近HDMI的USB口（N1暂不插电源），打开USB Burning Tool软件，左上角文件导入W大救砖包，勾选右边四个选项（擦除flash，擦除bootloader，烧录成功后重启，覆盖烧录密钥）

4. 短接图中的两个触点（不需要焊接，随便找个镊子或者金属丝连接就行），先点击开始，然后迅速插入N1电源，进度条自动开始走
   ![tearDown](/assets/1858764-921b48075b65b64e.png)

5. 一般情况下进行到21%会报错，报错后，点击停止，导入W大官改固件，确保右边所有选项只勾选了擦除flash，然后点击开始，等待烧录至100%成功即可关掉软件，拔掉usb线，至此N1救砖成功，又可以愉快地刷入其它固件了

6. 上一部如果失败，快速拔掉DC电源重新插上，进度条就可以继续走了