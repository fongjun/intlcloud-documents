为方便开发者调试和接入腾讯云游戏多媒体引擎产品，这里向您介绍适用于所有平台的鉴权密钥相关技术文档。


## 语音密钥后台部署
腾讯云游戏多媒体引擎提供鉴权密钥，用于实时语音及离线语音的鉴权，此文档为后台部署方案。
鉴权所用到的签名产生过程涉及到**明文**、**密钥**和**算法**。

### 明文
明文为以下字段的网络序拼接：

|字段描述    		| 类型/长度			| 值定义/备注|
| ---------------- |-------------------|--------------|
| cVer				|unsigned char/1	|版本号，填写数值：1		|
| wOpenIDLen		|unsigned short/2	|用户的帐号长度	|
| strOpenID			|wOpenIDLen		|用户的帐号字符	|
| dwSdkAppid		|unsigned short/4	|开发者的 SDKappid		|
| dwReserved1		|unsigned int/4		|填写数值：0				|
| dwExpTime		|unsigned int/4		|过期时刻（当前时间+有效期[单位：秒，建议 300 秒]）|
| dwReserved2		|unsigned int/4		|填写数值：-1 或者 0xFFFFFFFF|
| dwReserved3		|unsigned int/4		|填写数值：0				|
| wRoomIDLen		|unsigned short/2	|用户要进入的房间号码长度，如果是离线语音服务，请填写 0				|
| strRoomID			|wRoomIDLen		|用户要进入的房间号码字符				|

### 密钥
腾讯云 GME 控制台获取相关权限密钥。
![](https://main.qcloudimg.com/raw/5f360cf9f725960c08db4de4f06068da.png)

### 算法
TEA 对称加密算法。
总体建议，建议接入初期使用客户端部署方案，后面可优化为部署在游戏 App 后台。

|方案       		| 优点        		| 缺点																																|
| ------------- |-------------|-------------| 
| 后台部署    		|安全性高	|需要后台开发联调|
| 客户端部署      	|接入快速	|安全性低|

#### 后台部署使用方法
后台生成加密串后，下发给客户端，客户端适用于如下场景，调用 API:EnterRoom 进行进房操作时，将加密串传给进房参数的 authBuffer 字段。




### 算法加密详情
- 密钥：APPID 对应鉴权密钥的 md5 值，长度 16 字节
- 加密算法：TEA 加密
- 加密库及例子：附件 [authbuffer.zip](https://main.qcloudimg.com/raw/c8be793e20c85114499f52e0f8c29190.zip)

>!控制台修改密钥后，15 分钟 - 1 小时内生效，不建议频繁更换。



#### 加密方法	
- 密文中的数字转换成网络字节序（大端字节序）；
- 把密文拼接成一段字符串；
- 用 tea 加密对字符串加密，symmetry_encrypt 函数输出的字符串即为权限加密串；

>!不用要把二进制串转成 16 进制。


