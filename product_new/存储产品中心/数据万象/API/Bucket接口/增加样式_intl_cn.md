## 功能描述
该接口用于对某一个存储桶设置样式功能，后续上传到该存储桶的图片文件都会被添加指定的样式。

>?Bucket 接口目前频控存在限制要求，写请求最多1qps，读请求最多20qps。

## 请求
#### 请求示例

```
PUT /?style HTTP/1.1
Host: <BucketName-APPID>.pic.<Region>.myqcloud.com 
Date: GMT Date
Authorization: Auth String
<XML File>
```
>?Authorization: Auth String（详情参阅 [请求签名](https://intl.cloud.tencent.com/document/product/436/7778) 章节）。

#### 请求行

```
PUT /?style HTTP/1.1
```
该 API 接口接受 PUT 请求。

#### 请求头
##### 公共头部
该请求操作的实现使用公共请求头，了解公共请求头详情，请参阅 [公共请求头部](https://intl.cloud.tencent.com/document/product/436/7728) 章节。

#### 非公共头部
该请求操作无特殊的请求头部信息。
#### 请求体

```
<?xml version="1.0" encoding="UTF-8" ?>
<AddStyle>
  <StyleName>string</StyleName>
<StyleBody>string</StyleBody>
</AddStyle>
```

## 响应
#### 响应头
#### 公共响应头
该响应使用公共响应头，了解公共响应头详情，请参阅 [公共响应头部](https://intl.cloud.tencent.com/document/product/436/7729) 章节。
#### 特有响应头
该响应无特殊的响应头。
#### 响应体
该响应体返回为空。
