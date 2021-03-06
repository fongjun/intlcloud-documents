## 简介

本文档提供关于跨域访问的 API 概览以及 SDK 示例代码。

| API                                                          | 操作名       | 操作描述                       |
| ------------------------------------------------------------ | ------------ | ------------------------------ |
| [PUT Bucket cors](https://intl.cloud.tencent.com/document/product/436/8279) | 设置跨域配置 | 设置存储桶的跨域名访问权限     |
| [GET Bucket cors](https://intl.cloud.tencent.com/document/product/436/8274) | 查询跨域配置 | 查询存储桶的跨域名访问配置信息 |
| [DELETE Bucket cors](https://intl.cloud.tencent.com/document/product/436/8283) | 删除跨域配置 | 删除存储桶的跨域名访问配置信息 |

## 设置跨域配置

#### 功能说明

设置指定存储桶的跨域名访问配置信息（PUT Bucket cors）。

#### 方法原型
```C#
PutBucketCORSResult PutBucketCORS(PutBucketCORSRequest request);

void PutBucketCORS(PutBucketCORSRequest request, COSXML.Callback.OnSuccessCallback<CosResult> successCallback, COSXML.Callback.OnFailedCallback failCallback);
```

#### 请求示例
[//]: # (.cssg-snippet-put-bucket-cors)
```C#
CosXmlConfig config = new CosXmlConfig.Builder()
  .SetConnectionTimeoutMs(60000)  //设置连接超时时间，单位毫秒，默认45000ms
  .SetReadWriteTimeoutMs(40000)  //设置读写超时时间，单位毫秒，默认45000ms
  .IsHttps(true)  //设置默认 HTTPS 请求
  .SetAppid("1250000000") //设置腾讯云账户的账户标识 APPID
  .SetRegion("COS_REGION") //设置一个默认的存储桶地域
  .Build();

string secretId = "COS_SECRETID";   //云 API 密钥 SecretId
string secretKey = "COS_SECRETKEY"; //云 API 密钥 SecretKey
long durationSecond = 600;          //每次请求签名有效时长，单位为秒
QCloudCredentialProvider qCloudCredentialProvider = new DefaultQCloudCredentialProvider(secretId, 
  secretKey, durationSecond);

CosXml cosXml = new CosXmlServer(config, qCloudCredentialProvider);

try
{
  string bucket = "examplebucket-1250000000"; //格式：BucketName-APPID
  PutBucketCORSRequest request = new PutBucketCORSRequest(bucket);
  //设置签名有效时长
  request.SetSign(TimeUtils.GetCurrentTime(TimeUnit.SECONDS), 600);
  //设置跨域访问配置 CORS
  COSXML.Model.Tag.CORSConfiguration.CORSRule corsRule = 
    new COSXML.Model.Tag.CORSConfiguration.CORSRule();
  corsRule.id = "corsconfigureId";
  corsRule.maxAgeSeconds = 6000;
  corsRule.allowedOrigin = "http://intl.cloud.tencent.com";

  corsRule.allowedMethods = new List<string>();
  corsRule.allowedMethods.Add("PUT");

  corsRule.allowedHeaders = new List<string>();
  corsRule.allowedHeaders.Add("Host");

  corsRule.exposeHeaders = new List<string>();
  corsRule.exposeHeaders.Add("x-cos-meta-x1");

  request.SetCORSRule(corsRule);

  //执行请求
  PutBucketCORSResult result = cosXml.PutBucketCORS(request);
  //请求成功
  Console.WriteLine(result.GetResultInfo());
}
catch (COSXML.CosException.CosClientException clientEx)
{
  //请求失败
  Console.WriteLine("CosClientException: " + clientEx);
}
catch (COSXML.CosException.CosServerException serverEx)
{
  //请求失败
  Console.WriteLine("CosServerException: " + serverEx.GetInfo());
}
```

#### 参数说明
|参数名称|设置方法|描述|类型|
|-----|-----|-----|------|
|bucket|构造方法|存储桶名称，格式：BucketName-APPID|string|
|corsRule|SetCORSRule|设置存储桶的跨域访问配|CORSConfiguration.CORSRule|
|signStartTimeSecond|SetSign|签名有效期起始时间（Unix 时间戳），例如1557902800 |long|
|durationSecond|SetSign|签名有效期时长（单位为秒），例如签名有效时期为1分钟：60  |long|
|headerKeys|SetSign|签名的请求头|`List<string>`|
|queryParameterKeys|SetSign|签名的请求参数|`List<string>`|

#### 返回结果说明
通过 PutBucketCORSResult 返回请求结果。

|成员变量|类型|描述|
|-----|-----|----|
|httpCode|int|HTTP Code， [200, 300)之间表示操作成功，否则表示操作失败|

## 查询跨域配置

#### 功能说明

查询指定存储桶的跨域名访问配置信息（GET Bucket cors）。

#### 方法原型
```C#
GetBucketCORSResult GetBucketCORS(GetBucketCORSRequest request);

void GetBucketCORS(GetBucketCORSRequest request, COSXML.Callback.OnSuccessCallback<CosResult> successCallback, COSXML.Callback.OnFailedCallback failCallback);
```

#### 请求示例
[//]: # (.cssg-snippet-get-bucket-cors)
```C#
CosXmlConfig config = new CosXmlConfig.Builder()
  .SetConnectionTimeoutMs(60000)  //设置连接超时时间，单位毫秒，默认45000ms
  .SetReadWriteTimeoutMs(40000)  //设置读写超时时间，单位毫秒，默认45000ms
  .IsHttps(true)  //设置默认 HTTPS 请求
  .SetAppid("1250000000") //设置腾讯云账户的账户标识 APPID
  .SetRegion("COS_REGION") //设置一个默认的存储桶地域
  .Build();

string secretId = "COS_SECRETID";   //云 API 密钥 SecretId
string secretKey = "COS_SECRETKEY"; //云 API 密钥 SecretKey
long durationSecond = 600;          //每次请求签名有效时长，单位为秒
QCloudCredentialProvider qCloudCredentialProvider = new DefaultQCloudCredentialProvider(secretId, 
  secretKey, durationSecond);

CosXml cosXml = new CosXmlServer(config, qCloudCredentialProvider);

try
{
  string bucket = "examplebucket-1250000000"; //格式：BucketName-APPID
  GetBucketCORSRequest request = new GetBucketCORSRequest(bucket);
  //设置签名有效时长
  request.SetSign(TimeUtils.GetCurrentTime(TimeUnit.SECONDS), 600);
  //执行请求
  GetBucketCORSResult result = cosXml.GetBucketCORS(request);
  //存储桶的 CORS 配置信息
  CORSConfiguration conf = result.corsConfiguration;
}
catch (COSXML.CosException.CosClientException clientEx)
{
  //请求失败
  Console.WriteLine("CosClientException: " + clientEx);
}
catch (COSXML.CosException.CosServerException serverEx)
{
  //请求失败
  Console.WriteLine("CosServerException: " + serverEx.GetInfo());
}
```

#### 参数说明
|参数名称|设置方法|描述|类型|
|-----|-----|-----|------|
|bucket|构造方法|存储桶名称，格式：BucketName-APPID|string|
|signStartTimeSecond|SetSign|签名有效期起始时间（Unix 时间戳），例如1557902800 |long|
|durationSecond|SetSign|签名有效期时长（单位为秒），例如签名有效时期为1分钟：60  |long|
|headerKeys|SetSign|签名的请求头|`List<string>`|
|queryParameterKeys|SetSign|签名的请求参数|`List<string>`|

#### 返回结果说明
通过 GetBucketCORSResult 返回请求结果。

|成员变量|类型|描述|
|-----|-----|----|
|httpCode|int|HTTP Code， [200, 300)之间表示操作成功，否则表示操作失败|
|corsConfiguration|[CORSConfiguration](https://github.com/tencentyun/qcloud-sdk-dotnet/blob/master/QCloudCSharpSDK/COSXML/Model/Tag/CORSConfiguration.cs)|返回 Bucket 跨域资源共享配置的信息|

## 删除跨域配置

#### 功能说明

删除指定存储桶的跨域名访问配置（DELETE Bucket cors）。

#### 方法原型
```C#
DeleteBucketCORSResult DeleteBucketCORS(DeleteBucketCORSRequest request);

void DeleteBucketCORS(DeleteBucketCORSRequest request, COSXML.Callback.OnSuccessCallback<CosResult> successCallback, COSXML.Callback.OnFailedCallback failCallback);
```

#### 请求示例
[//]: # (.cssg-snippet-delete-bucket-cors)
```C#
CosXmlConfig config = new CosXmlConfig.Builder()
  .SetConnectionTimeoutMs(60000)  //设置连接超时时间，单位毫秒，默认45000ms
  .SetReadWriteTimeoutMs(40000)  //设置读写超时时间，单位毫秒，默认45000ms
  .IsHttps(true)  //设置默认 HTTPS 请求
  .SetAppid("1250000000") //设置腾讯云账户的账户标识 APPID
  .SetRegion("COS_REGION") //设置一个默认的存储桶地域
  .Build();

string secretId = "COS_SECRETID";   //云 API 密钥 SecretId
string secretKey = "COS_SECRETKEY"; //云 API 密钥 SecretKey
long durationSecond = 600;          //每次请求签名有效时长，单位为秒
QCloudCredentialProvider qCloudCredentialProvider = new DefaultQCloudCredentialProvider(secretId, 
  secretKey, durationSecond);

CosXml cosXml = new CosXmlServer(config, qCloudCredentialProvider);

try
{
  string bucket = "examplebucket-1250000000"; //格式：BucketName-APPID
  DeleteBucketCORSRequest request = new DeleteBucketCORSRequest(bucket);
  //设置签名有效时长
  request.SetSign(TimeUtils.GetCurrentTime(TimeUnit.SECONDS), 600);
  //执行请求
  DeleteBucketCORSResult result = cosXml.DeleteBucketCORS(request);
  //请求成功
  Console.WriteLine(result.GetResultInfo());
}
catch (COSXML.CosException.CosClientException clientEx)
{
  //请求失败
  Console.WriteLine("CosClientException: " + clientEx);
}
catch (COSXML.CosException.CosServerException serverEx)
{
  //请求失败
  Console.WriteLine("CosServerException: " + serverEx.GetInfo());
}
```

#### 参数说明

|参数名称|设置方法|描述|类型|
|-----|-----|-----|------|
|bucket|构造方法|存储桶名称，格式：BucketName-APPID|string|
|signStartTimeSecond|SetSign|签名有效期起始时间（Unix 时间戳），例如1557902800 |long|
|durationSecond|SetSign|签名有效期时长（单位为秒），例如签名有效时期为1分钟：60  |long|
|headerKeys|SetSign|签名的请求头|`List<string>`|
|queryParameterKeys|SetSign|签名的请求参数|`List<string>`|

#### 返回结果说明
通过 DeleteBucketCORSResult 返回请求结果。

|成员变量|类型|描述|
|-----|-----|----|
|httpCode|int|HTTP Code，[200, 300)之间表示操作成功，否则表示操作失败|

