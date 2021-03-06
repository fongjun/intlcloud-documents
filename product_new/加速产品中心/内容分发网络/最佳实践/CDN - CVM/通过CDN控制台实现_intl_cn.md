本文详细描述了通过 CDN 控制台，实现 CDN 加速 CVM 的整体操作流程和具体的操作方法。

## 前提条件
1. 完成腾讯云账号注册、实名认证。
2. 开通 CVM 服务，详情请参见 [开始使用 CVM](https://intl.cloud.tencent.com/document/product/213/2936)。


## 操作指南

### 添加域名

登录 [CDN 控制台](https://console.cloud.tencent.com/cdn)，在左侧导航栏中，单击【域名管理】进入域名管理页面，单击【添加域名】。
![](https://main.qcloudimg.com/raw/d301ff1eea5fe534ce09ec5964e8c82b.png)

### 域名配置
![](https://main.qcloudimg.com/raw/be5e2b251bbcbcc674242062684649c1.png)
1. 输入需要加速的**域名**
支持泛域名接入，如：*.test.com。支持域名批量接入，最多可添加10个域名。
域名需要满足以下条件：
	- 域名已经在工信部进行过备案
	- 域名尚未被接入过腾讯云 CDN
2. 选择域名的**所属项目**
此处的项目为腾讯云所有产品共享，您可以在 [项目管理](https://console.cloud.tencent.com/project) 中添加项目。
3. 选择**源站类型**并填写**源站设置**
源站类型的详细说明请参阅  [CDN 接入域名](https://intl.cloud.tencent.com/document/product/228/5734)。
源站设置中需填写您的 **CVM 外网地址**。

### 加速服务配置

根据您的业务需求选择**业务类型**、**基本配置**和**缓存过期配置**
![](https://main.qcloudimg.com/raw/6264633c18801547e4aece61a94009cb.png)
1. **业务类型**
业务类型选择决定了域名调度的资源平台，不同资源平台加速配置存在一定差异，请选择与您业务相匹配的业务类型：
	- 静态加速：适用于电商类、网站类、游戏图片类静态资源加速场景。
	- 下载加速：适用于游戏安装包、音视频原文件下载、手机固件分发等场景。
	- 流媒体点播加速：适用于音视频点播加速等场景。
2. **基本配置**
CDN 为您提供了过滤参数开关，您可以根据业务需要，控制是否对用户请求 URL 中 **“?”** 之后的参数进行过滤。您可以利用过滤参数灵活的进行版本控制，或对资源进行带有 Token 的鉴权。详情请参见 [过滤参数配置](https://intl.cloud.tencent.com/document/product/228/6291)。
3. **缓存过期配置**
缓存过期配置是指 CDN 加速节点在缓存您的业务内容时遵循的一套过期规则。详情请参见 [缓存过期配置](https://intl.cloud.tencent.com/document/product/228/6290)。

### 接入完成
输入**添加域名**页面所有配置后，单击【提交】完成添加域名操作，请耐心等待域名配置下发至全网节点，下发时间约5 - 10分钟。

### 配置CNAME
添加域名成功后，在**域名管理**页面，可以查看到 CDN 为您的域名分配的加速 CNAME，您需要前往接入域名的 DNS 服务商（如 Dnspod）处，为此域名添加一条 CNAME 记录，待 **DNS 配置生效后**，即可进行加速服务。详情请参见 [CNAME 配置](https://intl.cloud.tencent.com/doc/product/228/3121)。


>根据有关部门规定，源站为腾讯云 CVM 的加速域名，回源 HOST 配置的域名需要在腾讯云备案。更多详细内容请参见 [回源 HOST 配置](https://intl.cloud.tencent.com/document/product/228/6293)。

