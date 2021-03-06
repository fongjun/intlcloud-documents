## 操作场景

Linux 用户有时候需要进入到单用户模执行一些比较特殊的操作，例如密码管控，sshd 损坏等。本文档介绍主流 Linux 操作系统进入单用户模式的操作步骤。

## 操作步骤

### 判断操作系统类型

根据操作系统类型，选择不同的操作步骤。
 - CentOS 6 操作系统，执行 [CentOS 6 的步骤](#configCentOS6)。
 - CentOS 7 操作系统，执行 [CentOS 7 的步骤](#configCentOS7)。
 - Ubuntu 操作系统，执行 [Ubuntu 的步骤](#configUbuntu)。

<span id="configCentOS6"></span>
### CentOS 6

>? Centos 6 采用 GRUB 启动引导程序，以下操作步骤以 CentOS 6.9 为例，根据操作系统的版本不同，详细操作步骤略有区别。
> 
1. 远程登录 CVM 实例。
2. 执行以下命令，打开 `/etc/grub.conf` 文件。
```
vi /etc/grub.conf
```
3. 按 **I** 进入编辑模式。
4. 查找到 “GRUB_TIMEOUT” 默认启动项等候时间的参数，并根据实际需求修改其值为需设置的时间范围。
“GRUB_TIMEOUT” 默认值为5秒，为了避免因等候时间太短看不到开机界面，建议将其调整为60S或者更大。
>! 该项影响系统的启动时间，完成配置后，需将其修改回默认值。
5. 按 **Esc** 退出编辑模式，输入 **:wq**，按 **Enter**。
保存设置并退出 VI 编辑器。
6. 执行以下命令，重启服务器。
```
reboot
```
7. 等待约1分钟，并通过 [VNC 登录该 CVM 实例](https://cloud.tencent.com/document/product/213/5436#.E4.BD.BF.E7.94.A8-vnc-.E7.99.BB.E5.BD.95.E5.AE.9E.E4.BE.8B)。登录界面如下图所示：
![](https://main.qcloudimg.com/raw/82a82601e1545274c4f61c8f34f5c100.png)
8. 按任意键进入菜单。如下图所示：
![](https://main.qcloudimg.com/raw/6336b8fd579799108a5765b5b58e2a21.png)
9. 按 **e** 进入内核编辑界面，输入 **single**。如下图所示：
![](https://main.qcloudimg.com/raw/14168276d81a398702e80f9c83186869.png)
10. 按 **Enter**。如下图所示：
![](https://main.qcloudimg.com/raw/149eeb5776329a5db1ea42ae20cd316d.png)
11. 在如下图所示界面，按 **b** 进入单用户模式。
![](https://main.qcloudimg.com/raw/2d6d53de84cd78b3e88319b8538cec8e.png)
12. 执行以下命令，退出单用户模式。
```
exec /sbin/init
```

<span id="configCentOS7"></span>
### CentOS 7

>? 与 CentOS 6 不同，CentOS 7 之后版本采用 GRUB 2。以下操作步骤以 Centos 7.5 为例，根据操作系统的版本不同，详细操作步骤略有区别。
> 
1. 远程登录 CVM 实例。
2. 执行以下命令，打开 `/etc/default/grub` 文件。
```
vi /etc/default/grub
```
3. 按 **I** 进入编辑模式。
4. 查找到 “GRUB_TIMEOUT” 默认启动项等候时间的参数，并根据实际需求修改其值为需设置的时间范围。如下图所示：
“GRUB_TIMEOUT” 默认值为5秒，为了避免因等候时间太短看不到开机界面，建议将其调整为60S或者更大。
>! 该项影响系统的启动时间，完成配置后，需将其修改回默认值。
>
![](https://main.qcloudimg.com/raw/5ee3b8d8a4609ca846e3c1e929608b34.png)
5. 按 **Esc** 退出编辑模式，输入 **:wq**，按 **Enter**。
保存设置并退出 VI 编辑器。
6. 执行以下命令，重新编译和生成 grub.cfg 文件。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
返回结果如下图所示：
![](https://main.qcloudimg.com/raw/62da54e985f2f78efce045bb2da1e5e5.png)
7. 执行以下命令，重启服务器。
```
reboot
```
8. 等待约1分钟，并通过 [VNC 登录该 CVM 实例](https://cloud.tencent.com/document/product/213/5436#.E4.BD.BF.E7.94.A8-vnc-.E7.99.BB.E5.BD.95.E5.AE.9E.E4.BE.8B)。登录界面如下图所示：
![](https://main.qcloudimg.com/raw/95dba957dea2da680ffca516dc2b62b3.png)
9. 按 **e** 进入内核编辑界面，并将 **init=/bin/sh** 添加至红框处。如下图所示：
![](https://main.qcloudimg.com/raw/81173f4c723809f1b733a51a2eb002d5.png)
7. 按 **Ctrl+X**，启动并进入单用户模式。如下图所示：
![](https://main.qcloudimg.com/raw/b9004e2a1d58a9a09316cf2a8a907399.png)
8. 执行以下命令，退出单用户模式。
```
exec /sbin/init
```

<span id="configUbuntu"></span>
### Ubuntu 

>? 以下操作步骤以 Ubuntu 16.04 为例，根据操作系统的版本不同，详细操作步骤略有区别。
>
1. 远程登录 CVM 实例。
2. 执行以下命令，打开 `/etc/default/grub` 文件。
```
sudo vi /etc/default/grub
```
3. 按 **I** 进入编辑模式。
4. 查找到 “GRUB_TIMEOUT” 默认启动项等候时间的参数，并根据实际需求修改其值为需设置的时间范围。如下图所示：
“GRUB_TIMEOUT” 默认值为5秒，为了避免因等候时间太短看不到开机界面，建议将其调整为60S或者更大。
>! 
> - 该项影响系统的启动时间，完成配置后，需将其修改回默认值。
> - Ubuntu 的默认帐户非 root，请注意使用 sudo 命令。
> 
```
sudo vi /etc/default/grub
```
![](https://main.qcloudimg.com/raw/65553c2d5a01113e33b93caa93485dae.png)
3. 执行以下命令，重新编译和生成 grub.cfg 文件。
```
sudo update-grub
```
返回结果如下图所示：
![](https://main.qcloudimg.com/raw/9e685185ef67e7129ce34b11b5a16061.png)
4. 执行以下命令，重启服务器。
```
sudo reboot
```
5. 等待约1分钟，并通过 [VNC 登录该 CVM 实例](https://cloud.tencent.com/document/product/213/5436#.E4.BD.BF.E7.94.A8-vnc-.E7.99.BB.E5.BD.95.E5.AE.9E.E4.BE.8B)。登录界面如下图所示：
![](https://main.qcloudimg.com/raw/4893e2a2ed32bbe4241b33b468bdb8cf.png)
6. 按 **e** 进入内核编辑界面，并将 **rw single init=/bin/bash** 添加至红框处。如下图所示：
![](https://main.qcloudimg.com/raw/0879dd0c8c7a720542352a0722f9b9a7.png)
7. 按 **Ctrl+X**，启动并进入单用户模式。如下图所示：
![](https://main.qcloudimg.com/raw/ffc6c3cf07a9254fdcb4f6326c3daf75.png)

