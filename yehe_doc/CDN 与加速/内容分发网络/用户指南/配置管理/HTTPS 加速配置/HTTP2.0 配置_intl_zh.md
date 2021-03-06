## 配置场景
已配置证书启用 HTTPS 加速的域名，可自助开启 HTTP2.0 协议支持。
>目前仅支持 HTTP2.0 访问，暂不支持 HTTP2.0 协议回源。

## 配置指南
### 查看配置
登录 [CDN 控制台](https://console.cloud.tencent.com/cdn)，在菜单栏里选择【域名管理】，单击域名右侧【管理】，即可进入域名配置页面最后一栏【高级配置】中，可看到【HTTP2.0 配置】，默认情况下为开启状态：
![](https://main.qcloudimg.com/raw/b4e746132ced3995845c0af895408324.png)

###  修改配置
通过单击开关，可对 HTTP2.0 配置进行开启或关闭操作，删除证书配置后，HTTP2.0 配置会同步失效：
![](https://main.qcloudimg.com/raw/78075916f06b8cc5b03a75ac86786fe3.png)

>若域名的服务区域为全球，则配置的 HTTP2.0 会全球生效，暂不支持境内、境外分别配置。

