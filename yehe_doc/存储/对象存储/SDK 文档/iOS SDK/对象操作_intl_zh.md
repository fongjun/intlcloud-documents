## 简介

本文档重点提供关于对象的简单操作、分块操作以及其他操作相关的 API 概览以及 SDK 示例代码，并且举例如何使用。

- 我们假设您已经按照 [快速入门](https://intl.cloud.tencent.com/document/product/436/8629) 文档中的指引完成了 SDK 下载、安装和初始化的过程。
- 查询时建议使用 Command+F 搜索到想要查询的接口，然后看我们给出的接口简单说明，复制示例到您的工程中运行。

>如果需要了解接口的功能或者参数的意义，建议直接查看代码里的注释，Xcode 支持通过三指轻按、Force-touch 重按或者将鼠标停留在变量上，按 Control+Command+D 的方式查看它的释义。   

**简单操作**

| API                                                          | 操作名         | 操作描述                                 |
| ------------------------------------------------------------ | -------------- | ---------------------------------------- |
| [GET Bucket（List Object）](https://cloud.tencent.com/document/product/436/7734) | 查询对象列表   | 查询存储桶下的部分或者全部对象           |
| [PUT Object](https://cloud.tencent.com/document/product/436/7749) | 上传对象       | 上传一个对象至存储桶                     |
| [HEAD Object](https://cloud.tencent.com/document/product/436/7745) | 查询对象元数据 | 查询对象的元数据信息                     |
| [GET Object](https://cloud.tencent.com/document/product/436/7753) | 下载对象       | 下载一个对象至本地                       |
| [Options Object](https://cloud.tencent.com/document/product/436/8288) | 预请求跨域配置 | 用预请求来确认是否可以发送真正的跨域请求 |
| [PUT Object - Copy](https://cloud.tencent.com/document/product/436/10881) | 设置对象复制   | 复制文件到目标路径                       |
| [DELETE Object](https://cloud.tencent.com/document/product/436/7743) | 删除单个对象   | 在存储桶中删除指定对象                   |
| [DELETE Multiple Object](https://cloud.tencent.com/document/product/436/8289) | 删除多个对象   | 在存储桶中批量删除对象                   |

**分块操作**

| API                                                          | 操作名         | 操作描述                             |
| ------------------------------------------------------------ | -------------- | ------------------------------------ |
| [List Multipart Uploads](https://cloud.tencent.com/document/product/436/7736) | 查询分块上传   | 查询正在进行中的分块上传信息         |
| [Initiate Multipart Upload](https://cloud.tencent.com/document/product/436/7746) | 初始化分块上传 | 初始化分块上传任务                   |
| [Upload Part](https://cloud.tencent.com/document/product/436/7750) | 上传分块       | 分块上传文件                         |
| [Upload Part - Copy](https://cloud.tencent.com/document/product/436/8287) | 复制分块       | 将其他对象复制为一个分块             |
| [List Parts](https://cloud.tencent.com/document/product/436/7747) | 查询已上传块   | 查询特定分块上传操作中的已上传的块   |
| [Complete Multipart Upload](https://cloud.tencent.com/document/product/436/7742) | 完成分块上传   | 完成整个文件的分块上传               |
| [Abort Multipart Upload](https://cloud.tencent.com/document/product/436/7740) | 终止分块上传   | 终止一个分块上传操作并删除已上传的块 |

**其他操作**

| API                                                          | 操作名       | 操作描述                           |
| ------------------------------------------------------------ | ------------ | ---------------------------------- |
| [POST Object restore](https://cloud.tencent.com/document/product/436/12633) | 恢复归档对象 | 将归档类型的对象取回访问           |
| [PUT Object acl](https://cloud.tencent.com/document/product/436/7748) | 设置对象 ACL | 设置存储桶中某个对象的访问控制列表 |
| [GET Object acl](https://cloud.tencent.com/document/product/436/7744) | 查询对象 ACL | 查询对象的访问控制列表             |

## 高级接口（推荐）

该小节中封装了上传和复制的高级接口，用户只需要设置相应的参数，该接口内部会根据文件大小决定是进行简单上传/复制，还是分块上传/复制，使用接口前请确认已完成了 [快速入门](https://intl.cloud.tencent.com/document/product/436/8629) 中指引的初始化步骤。

### 上传对象

可以参见快速入门中的 [上传对象](https://intl.cloud.tencent.com/document/product/436/8629#.E4.B8.8A.E4.BC.A0.E5.AF.B9.E8.B1.A1) 环节。

#### 上传对象时的断点续传

>断点续传只支持上传沙盒中的文件。

对于大于1MB的对象，SDK 都会以分块上传的方式进行上传，即将对象切分成多个1MB大小的分块，并行（最大并行数为4）进行上传。对于每个已经完成上传的分块，后台服务器都会将其保存起来，这是上传时候断点续传的基础。  

在分块上传的过程中，初始化分块上传完成、或者是主动取消上传的话，都会产生一个用于恢复上传的 resumeData，可以通过这个 resumetData 重新生成一个上传请求，再继续用这个上传请求上传，即可接着继续上传，并且进度也会相应叠加。可以参照下面获取 ResumeData 的例子：


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-transfer-upload-object)
```objective-C
QCloudCOSXMLUploadObjectRequest* put = [QCloudCOSXMLUploadObjectRequest new];
put.object = @"exampleobject";
put.bucket = @"examplebucket-1250000000";
put.body = [@"testFileContent" dataUsingEncoding:NSUTF8StringEncoding];
//设置一些上传的参数
put.initMultipleUploadFinishBlock = ^(QCloudInitiateMultipartUploadResult * multipleUploadInitResult,
    QCloudCOSXMLUploadObjectResumeData resumeData) {
    //在初始化分块上传完成以后会回调该 block，在这里可以获取 resumeData，
    //并且可以通过 resumeData 生成一个分块上传的请求
    QCloudCOSXMLUploadObjectRequest* request = [QCloudCOSXMLUploadObjectRequest
        requestWithRequestData:resumeData];
};
[put setSendProcessBlock:^(int64_t bytesSent, int64_t totalBytesSent,
    int64_t totalBytesExpectedToSend) {
    NSLog(@"upload %lld totalSend %lld aim %lld", bytesSent, totalBytesSent,
        totalBytesExpectedToSend);
}];
[put setFinishBlock:^(QCloudUploadObjectResult *result, NSError* error) {
    //可以从 result 获取结果
}];

[[QCloudCOSTransferMangerService defaultCOSTransferManager] UploadObject:put];
//舍弃一个分块上传并删除已上传的块
[put abort:^(id outputObject, NSError *error) {
//
}];

//•••在完成了初始化，并且上传没有完成前
NSError* error;
//这里是主动调用取消，并且产生 resumetData 的例子
QCloudCOSXMLUploadObjectResumeData resumeData = [put cancelByProductingResumeData:&error];
QCloudCOSXMLUploadObjectRequest* request = nil;
if (resumeData) {
    request = [QCloudCOSXMLUploadObjectRequest requestWithRequestData:resumeData];
}
//生成的用于恢复上传的请求可以直接上传
[[QCloudCOSTransferMangerService defaultCOSTransferManager] UploadObject:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-transfer-upload-object)
```swift
let uploadRequest = QCloudCOSXMLUploadObjectRequest<AnyObject>.init();
let dataBody:NSData? = "wrwrwrwrwrw".data(using: .utf8) as NSData?;
uploadRequest.body = dataBody!;
uploadRequest.bucket = "examplebucket-1250000000";
uploadRequest.object = "exampleobject";
//设置上传参数
uploadRequest.initMultipleUploadFinishBlock = {(multipleUploadInitResult,resumeData) in
    //在初始化分块上传完成以后会回调该 block，在这里可以获取 resumeData，并且可以通过 resumeData 生成一个分块上传的请求
    let resumeUploadRequest = QCloudCOSXMLUploadObjectRequest<AnyObject>.init(request: resumeData as Data?);
}
uploadRequest.sendProcessBlock = {(bytesSent , totalBytesSent , totalBytesExpectedToSend) in
    
}
uploadRequest.setFinish { (result, error) in
    if error != nil{
        print(error!)
    }else{
        //从 result 中获取请求的结果
        print(result!);
    }}

QCloudCOSTransferMangerService.defaultCOSTransferManager().uploadObject(uploadRequest);

//•••在完成了初始化，并且上传没有完成前
var error:NSError?;
    //这里是主动调用取消，并且产生 resumetData 的例子
do {
    let resumedData = try uploadRequest.cancel(byProductingResumeData: &error);
        var resumeUploadRequest:QCloudCOSXMLUploadObjectRequest<AnyObject>;
             resumeUploadRequest = QCloudCOSXMLUploadObjectRequest<AnyObject>.init(request: resumedData as Data?);
             //生成的用于恢复上传的请求可以直接上传
    if resumeUploadRequest != nil {
        QCloudCOSTransferMangerService.defaultCOSTransferManager().uploadObject(resumeUploadRequest!);
    }
             
} catch  {
    print("resumeData 为空");
    return;
}
```

注意的是，按照分块上传的运行原理，只有当一个分块上传完了，那么后台服务器才会将该分块记录下来，并且叠加进度。并且以下几种情况无法进行断点续传，而是重新开始一次上传过程：

- 上传的对象小于1MB，没有进行分块上传。
- 没有使用 QCloudCOSXMLUploadObjectRequest 类进行上传，而是直接使用简单上传接口。
- 取消生成 resumeData 时候初始化分块上传还没有完成（完成初始化上传的回调还没有调用）。

### 下载对象

可以参见快速入门中的 [下载对象](https://intl.cloud.tencent.com/document/product/436/8629#.E4.B8.8B.E8.BD.BD.E5.AF.B9.E8.B1.A1) 章节。

### 复制对象

#### 说明

先初始化一个 QCloudCOSXMLCopyObjectRequest 对象，然后调用 QCloudCOSTransferMangerService 的 CopyObject 方法即可。注意对于比较大的文件，将会使用分块复制的方式进行复制。这个过程对于用户是没有感知的。  

>如果是跨地域复制，这里使用的 transferManager 所在的 region 必须为目标桶所在的 region。

#### QCloudCOSXMLCopyObjectRequest 参数说明

| 参数名称          | 描述                                                         | 类型                  | 必填 |
| ----------------- | ------------------------------------------------------------ | --------------------- | ---- |
| bucket            | 存储桶名，格式为 &lt;BucketName-APPID&gt;，<br>例如 examplebucket-1250000000 | NSString *            | 是   |
| sourceBucket      | 复制的源文件所在 Bucket                                      | NSString *            | 是   |
| sourceObject      | 复制的源文件的对象名，key                                    | NSString *            | 是   |
| sourceAPPID       | 复制的源文件的 APPID                                         | NSString *            | 是   |
| sourceRegion      | 复制的源文件所在的区域                                       | NSString *            | 是   |
| metadataDirective | 是否拷贝元数据，枚举值：Copy，Replaced，默认值 Copy。<br><li>假如标记为 Copy，忽略 Header 中的用户元数据信息直接复制<br><li>假如标记为 Replaced，按 Header 信息修改元数据。当目标路径和原路径一致，即用户试图修改元数据时，必须为 Replaced | NSString *            | 否   |
| storageClass      | 对象的存储级别，枚举值：<br><li>STANDARD（QCloudCOSStorageStandard）<br><li>STANDARD_IA（QCloudCOSStorageStandardIA）<br>默认值：STANDARD（QCloudCOSStorageStandard） | QCloudCOSStorageClass | 否   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-transfer-copy-object)
```objective-c
QCloudCOSXMLCopyObjectRequest* request = [[QCloudCOSXMLCopyObjectRequest alloc] init];

request.bucket = @"examplebucket-1250000000";//目的 <BucketName-APPID>，需要是公有读或者在当前账号有权限
request.object = @"exampleobject";//目的文件名称
//文件来源 <BucketName-APPID>，需要是公有读或者在当前账号有权限
request.sourceBucket = @"sourcebucket-1250000000";
request.sourceObject = @"sourceObject";//源文件名称
request.sourceAPPID = @"1250000000";//源文件的 APPID
request.sourceRegion= @"COS_REGION";//来源的地域

[request setFinishBlock:^(QCloudCopyObjectResult* result, NSError* error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

//注意如果是跨地域复制，这里使用的 transferManager 所在的 region 必须为目标桶所在的 region
[[QCloudCOSTransferMangerService defaultCOSTransferManager] CopyObject:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-transfer-copy-object)
```swift
let copyRequest =  QCloudCOSXMLCopyObjectRequest.init();
copyRequest.bucket = "examplebucket-1250000000";//目的 <BucketName-APPID>，需要是公有读或者在当前账号有权限
copyRequest.object = "exampleobject";//目的文件名称
//文件来源 <BucketName-APPID>，需要是公有读或者在当前账号有权限
copyRequest.sourceBucket = "sourcebucket-1250000000";
copyRequest.sourceObject = "sourceObject";//源文件名称
copyRequest.sourceAPPID = "1250000000";//源文件的 APPID
copyRequest.sourceRegion = "COS_REGION";//来源的地域
copyRequest.setFinish { (copyResult, error) in
    if error != nil{
        print(error!);
    }else{
        print(copyResult!);
    }
}
//注意如果是跨地域复制，这里使用的 transferManager 所在的 region 必须为目标桶所在的 region
QCloudCOSTransferMangerService.defaultCOSTransferManager().copyObject(copyRequest);
```

## 简单操作

### 查询对象列表

#### 功能说明

查询存储桶下的部分或者全部对象。

#### 方法原型

进行存储桶操作之前，我们需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudGetBucketRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下:

1. 实例化 QCloudGetBucketRequest，填入需要的参数。    
2. 调用 QCloudCOSXMLService 对象中的 GetBucket 方法发出请求。    
3. 从回调的 finishBlock 中的 QCloudListBucketResult 获取具体内容。   

#### QCloudGetBucketRequest 参数说明

| 参数名称     | 描述                                                         | 类型       | 必填 |
| ------------ | ------------------------------------------------------------ | ---------- | ---- |
| bucket       | 存储桶名，可在 [COS 控制台](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; ，<br>例如 examplebucket-1250000000 | NSString * | 是   |
| prefix       | 前缀匹配，用来规定返回的文件前缀地址                         | NSString * | 否   |
| delimiter    | 定界符为一个符号：<br><li>如果有 Prefix，则将 Prefix 到 delimiter 之间的相同路径归为一类，定义为 Common Prefix，然后列出所有 Common Prefix<br><li>如果没有 Prefix，则从路径起点开始。可以将其理解为结束的符号，例如如果想要结尾是 A 的结果，那么将 delimiter 设置为 A 即可| NSString * | 否   |
| encodingType | 规定返回值的编码方式，可选值：url                            | NSString * | 否   |
| marker       | 默认以 UTF-8 二进制顺序列出条目，所有列出条目从 marker 开始  | NSString * | 否   |
| maxKeys      | 单次返回的最大条目数量，默认1000，最大为1000                 | int        | 否   |

#### 返回结果说明

QCloudListBucketResult 参数说明

| 参数名称       | 描述                                                         | 类型                             |
| -------------- | ------------------------------------------------------------ | -------------------------------- |
| name           | 存储桶名信息                                                 | NSString *                       |
| prefix         | 前缀匹配，用来规定响应返回的文件前缀地址                     | NSString *                       |
| marker         | 默认以 UTF-8 二进制顺序列出条目，所有列出条目从 marker 开始  | NSString *                       |
| nextMarker     | 假如返回条目被截断，则返回 NextMarker 就是下一个条目的起点   | NSString *                       |
| maxKeys        | 单次响应请求内返回结果的最大条目数量                         | int                              |
| delimiter      | 定界符为一个符号：<br><li>如果有 Prefix，则将 Prefix 到 delimiter 之间的相同路径归为一类，定义为 Common Prefix，然后列出所有 Common Prefix<br><li>如果没有 Prefix，则从路径起点开始 | NSString *                       |
| isTruncated    | 响应请求条目是否被截断                                       | BOOL                             |
| contents       | 每一个对象的信息                                             | NSArray<QCloudBucketContents*> * |
| commonPrefixes | 将 Prefix 到 delimiter 之间的相同路径归为一类，定义为 Common Prefix | NSArray<QCloudCommonPrefixes*> * |

QCloudBucketContents 参数说明

| 参数名称     | 描述                                                      | 类型                     |
| ------------ | --------------------------------------------------------- | ------------------------ |
| key          | 对象的 key                                                | NSString *               |
| lastModified | 说明对象最后被修改的时间                                  | NSString *               |
| eTag         | 文件的 MD-5 算法校验值                                    | NSString *               |
| size         | 文件大小，单位是 Byte                                     | int                      |
| owner        | 存储桶持有者信息                                          | QCloudBucketOwner *      |
| storageClass | Object 的存储级别，枚举值：STANDARD，STANDARD_IA，ARCHIVE | QCloudCOSStorageClass  * |

QCloudBucketOwner 参数说明

| 参数名称    | 描述                | 类型       |
| ----------- | ------------------- | ---------- |
| identifier  | Bucket 的 APPID     | NSString * |
| displayName | Object 持有者的名称 | NSString * |



QCloudCommonPrefixes 参数说明

| 参数名称 | 描述               | 类型       |
| -------- | ------------------ | ---------- |
| prefix   | 单条 Common 的前缀 | NSString * |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-get-bucket)
```objective-c
QCloudGetBucketRequest* request = [QCloudGetBucketRequest new];
request.bucket = @"examplebucket-1250000000";
request.maxKeys = 1000;

[request setFinishBlock:^(QCloudListBucketResult * result, NSError* error) {
    // result 返回具体信息
}];

[[QCloudCOSXMLService defaultCOSXML] GetBucket:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-get-bucket)
```swift
let getBucketReq = QCloudGetBucketRequest.init();
getBucketReq.bucket = "examplebucket-1250000000";
getBucketReq.maxKeys = 1000;
getBucketReq.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        print( result!.commonPrefixes);
    }}
QCloudCOSXMLService.defaultCOSXML().getBucket(getBucketReq);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 上传对象

#### 功能说明

上传对象到指定的存储桶中（Put Object），简单上传仅限于小文件（20MB以下）。简单上传支持从内存中上传文件。

>当前访问策略条目限制为1000条，如果您不需要进行对象 ACL 控制，请在上传时不要设置，默认继承 Bucket 权限。

#### QCloudPutObjectRequest 参数说明

| 参数名称           | 说明                                                         | 类型                  | 必填 |
| ------------------ | ------------------------------------------------------------ | --------------------- | ---- |
| Object             | 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/picture.jpg`中，对象键为 doc/picture.jpg，详情请参见 [对象概述](https://intl.cloud.tencent.com/document/product/436/13324) | NSString *            | 是   |
| bucket             | 存储桶名，格式为 &lt;BucketName-APPID&gt; ，例如 examplebucket-1250000000 | NSString *            | 是   |
| body               | 如果文件存放在硬盘中，这里是需要上传的文件的路径，填入`NSURL * 类型变量`。如果文件存放在内存中，则这里可以填入包含文件二进制数据的`NSData * 类型变量` | BodyType              | 是   |
| storageClass       | 对象的存储级别，枚举值：STANDARD（QCloudCOSStorageStandard），STANDARD_IA（QCloudCOSStorageStandardIA），ARCHIVE（QCloudCOSStorageARCHIVE）。默认值：STANDARD（QCloudCOSStorageStandard） | QCloudCOSStorageClass | 否   |
| cacheControl       | RFC 2616中定义的缓存策略，将作为 Object 元数据保存            | NSString *            | 否   |
| contentDisposition | RFC 2616中定义的文件名称，将作为 Object 元数据保存            | NSString *            | 否   |
| expect             | 当使用 expect=@"100-Continue"时，在收到服务端确认后才会发送请求内容 | NSString *            | 否   |
| expires            | RFC 2616中定义的过期时间，将作为 Object 元数据保存            | NSString *            | 否   |
| accessControlList  | 定义 Object 的 ACL 属性，有效值：private，public-read，default。默认值：default（继承 Bucket 权限）。<br>注意：当前访问策略条目限制为1000条，如果您不需要进行 Object ACL 控制，请填 default 或者此项不进行设置，默认继承 Bucket 权限 | NSString *            | 否   |
| grantRead          | 赋予被授权者读的权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| grantWrite         | 授予被授权者写的权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| grantFullControl   | 授予被授权者读写权限，格式：id="OwnerUin"                    | NSString *            | 否   |

#### 示例    


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-put-object)
```objective-C
QCloudPutObjectRequest* put = [QCloudPutObjectRequest new];
put.object = @"exampleobject";
put.bucket = @"examplebucket-1250000000";
put.body =  [@"testFileContent" dataUsingEncoding:NSUTF8StringEncoding];

[put setFinishBlock:^(id outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML] PutObject:put];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-put-object)
```swift
let putObject = QCloudPutObjectRequest<AnyObject>.init();
putObject.bucket = "examplebucket-1250000000";
let dataBody:NSData? = "wrwrwrwrwrw".data(using: .utf8) as NSData?;
putObject.body =  dataBody!;
putObject.object = "exampleobject";
putObject.finishBlock = {(result,error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().putObject(putObject);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码](https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 查询对象元数据

#### 功能说明

查询对象的元数据信息。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudHeadObjectRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudHeadObjectRequest，填入需要的参数。    
2. 调用 QCloudCOSXMLService 对象中的 HeadObject 方法发出请求。    
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。   

#### QCloudHeadObjectRequest 参数说明

| 参数名称        | 描述                                                         | 类型       | 必填 |
| --------------- | ------------------------------------------------------------ | ---------- | ---- |
| Object          | 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/text.txt`中，对象键为 doc/text.txt。更详细的描述可以参见 [对象描述](https://intl.cloud.tencent.com/document/product/436/13324) | NSString * | 是   |
| bucket          | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; ，<br>例如 examplebucket-1250000000 | NSString * | 是   |
| ifModifiedSince | 如果文件修改时间晚于指定时间，才返回文件内容。否则返回 304 （not modified） | NSString * | 是   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-head-object)
```objective-c
QCloudHeadObjectRequest* headerRequest = [QCloudHeadObjectRequest new];
headerRequest.object = @"exampleobject";
headerRequest.bucket = @"examplebucket-1250000000";

[headerRequest setFinishBlock:^(NSDictionary* result, NSError *error) {
    // result 返回具体信息
}];

[[QCloudCOSXMLService defaultCOSXML] HeadObject:headerRequest];
```
Swift 代码示例：

[//]: # (.cssg-snippet-swift-head-object)
```swift
let headObject = QCloudHeadObjectRequest.init();
headObject.bucket = "examplebucket-1250000000";
headObject.object  = "exampleobject";
headObject.finishBlock =  {(result,error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().headObject(headObject);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 下载对象

#### 功能说明

下载一个对象至本地。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化QCloudGetObjectRequest对象，填入需要的参数。    
2. 调用 QCloudCOSXMLService 对象中的方法发出请求。    
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。   

#### QCloudGetObjectRequest 请求参数说明

| 参数名称                   | 描述                                                         | 类型       | 必填 |
| -------------------------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket                     | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;，<br>例如 examplebucket-1250000000 | NSString * | 是   |
| object                     | 对象名                                                       | NSString * | 是   |
| range                      | RFC 2616 中定义的指定文件下载范围，以字节（bytes）为单位     | NSString * | 否   |
| ifModifiedSince            | 如果文件修改时间晚于指定时间，才返回文件内容。否则返回412 （not modified） | NSString * | 否   |
| responseContentType        | 设置响应头部中的 Content-Type 参数                           | NSString * | 否   |
| responseContentLanguage    | 设置响应头部中的 Content-Language 参数                       | NSString * | 否   |
| responseContentExpires     | 设置响应头部中的 Content-Expires 参数                        | NSString * | 否   |
| responseCacheControl       | 设置响应头部中的 Cache-Control 参数                          | NSString * | 否   |
| responseContentDisposition | 设置响应头部中的 Content-Disposition 参数                  | NSString * | 否   |
| responseContentEncoding    | 设置响应头部中的 Content-Encoding 参数                       | NSString * | 否   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-get-object)
```objective-c
QCloudGetObjectRequest* request = [QCloudGetObjectRequest new];
//设置下载的路径 URL，如果设置了，文件将会被下载到指定路径中
//如果未设置该参数，那么文件将会被下载至内存里，存放在在 finishBlock 的 outputObject 里
request.downloadingURL = [NSURL URLWithString:QCloudTempFilePathWithExtension(@"downding")];
request.object = @"exampleobject";
request.bucket = @"examplebucket-1250000000";

[request setFinishBlock:^(id outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];
[request setDownProcessBlock:^(int64_t bytesDownload, int64_t totalBytesDownload,
    int64_t totalBytesExpectedToDownload) {
    //下载过程中的进度
}];

[[QCloudCOSXMLService defaultCOSXML] GetObject:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-get-object)
```swift
let getObject = QCloudGetObjectRequest.init();
getObject.bucket = "examplebucket-1250000000";
getObject.object = "exampleobject";
getObject.downloadingURL = URL.init(string: NSTemporaryDirectory())!.appendingPathComponent(getObject.object);
getObject.finishBlock = {(result,error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }};
getObject.downProcessBlock = {(bytesDownload, totalBytesDownload,  totalBytesExpectedToDownload) in
    print("totalBytesDownload:\(totalBytesDownload) totalBytesExpectedToDownload:\(totalBytesExpectedToDownload)");
}
QCloudCOSXMLService.defaultCOSXML().getObject(getObject);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 预请求跨域配置

#### 功能说明

用预请求来确认是否可以发送真正的跨域请求。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudOptionsObjectRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudOptionsObjectRequest，填入需要设置的对象名、存储桶名、模拟跨域访问请求的 HTTP 方法和模拟跨域访问允许的访问来源。    
2. 调用 QCloudCOSXMLService 对象中的方法发出请求。    
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。   

#### QCloudOptionsObjectRequest 参数说明

| 参数名称                   | 描述                                                         | 类型                        | 必填 |
| -------------------------- | ------------------------------------------------------------ | --------------------------- | ---- |
| object                     | 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/picture.jpg`中，对象键为 doc/picture.jpg，详情请参见 [对象概述](https://intl.cloud.tencent.com/document/product/436/13324) | NSString *                  | 是   |
| bucket                     | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; <br>例如 examplebucket-1250000000 | NSString *                  | 是   |
| accessControlRequestMethod | 模拟跨域访问的请求HTTP方法                                   | NSArray&lt;NSString`*`> *   | 是   |
| origin                     | 模拟跨域访问允许的访问来源，支持通配符`*`，<br>格式为`协议://域名[:端口]`，<br>例如`http://www.qq.com` | NSString *                  | 是   |
| allowedHeader              | 在发送 OPTIONS 请求时告知服务端，接下来的请求可以使用哪些自定义的 HTTP 请求头部，支持通配符`*` | NSArray&lt;NSString `*` > * | 否   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-option-object)
```objective-c
QCloudOptionsObjectRequest* request = [[QCloudOptionsObjectRequest alloc] init];
request.bucket =@"examplebucket-1250000000";
request.origin = @"http://cloud.tencent.com";
request.accessControlRequestMethod = @"GET";
request.accessControlRequestHeaders = @"host";
request.object = @"exampleobject";

[request setFinishBlock:^(id outputObject, NSError* error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML] OptionsObject:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-option-object)
```swift
let optionsObject = QCloudOptionsObjectRequest.init();
optionsObject.object = "exampleobject";
optionsObject.origin = "http://www.qcloud.com";
optionsObject.accessControlRequestMethod = "GET";
optionsObject.accessControlRequestHeaders = "origin";
optionsObject.bucket = "examplebucket-1250000000";
optionsObject.finishBlock = {(result,error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().optionsObject(optionsObject);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 设置对象复制

#### 功能说明

复制文件到目标路径。

#### 请求示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-copy-object)
```Objective-C
QCloudPutObjectCopyRequest* request = [[QCloudPutObjectCopyRequest alloc] init];
request.bucket = @"examplebucket-1250000000";
request.object = @"exampleobject";
//源对象所在的路径
request.objectCopySource = @"sourcebucket-1250000000.cos.COS_REGION.myqcloud.com/sourceObject";
[request setFinishBlock:^(QCloudCopyObjectResult * _Nonnull result, NSError * _Nonnull error) {
    //result 返回具体信息
}];
[[QCloudCOSXMLService defaultCOSXML]  PutObjectCopy:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-copy-object)
```swift
let putObjectCopy = QCloudPutObjectCopyRequest.init();
putObjectCopy.bucket = "examplebucket-1250000000";
putObjectCopy.object = "exampleobject";
putObjectCopy.objectCopySource = "sourcebucket-1250000000.cos.COS_REGION.myqcloud.com/sourceObject";
putObjectCopy.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().putObjectCopy(putObjectCopy);
```

| 参数名称          | 描述                                                         | 类型                  | 必填 |
| ----------------- | ------------------------------------------------------------ | --------------------- | ---- |
| bucket            | 目标存储桶名，格式为 &lt;BucketName-APPID&gt;，<br>例如 examplebucket-1250000000 | NSString *            | 是   |
| object            | 目标文件的对象键                                        | NSString *            | 是   |
| objectCopySource  | 复制的源文件的路径                                           | NSString *            | 是   |
| metadataDirective | 是否拷贝元数据，枚举值：Copy，Replaced，默认值 Copy。<br><li>假如标记为 Copy，忽略 Header 中的用户元数据信息直接复制<br><li>假如标记为 Replaced，按 Header 信息修改元数据。当目标路径和原路径一致，即用户试图修改元数据时，必须为 Replaced | NSString *            | 否   |
| storageClass      | 对象的存储级别，枚举值：<br><li>STANDARD（QCloudCOSStorageStandard）<br><li>STANDARD_IA（QCloudCOSStorageStandardIA）<br>默认值：STANDARD（QCloudCOSStorageStandard） | QCloudCOSStorageClass | 否   |
| accessControlList | 定义 Object 的 ACL 属性，有效值：private，public-read，default。默认值：default（继承 Bucket 权限）。<br>注意：当前访问策略条目限制为1000条，如果您无需进行 Object ACL 控制，请填 default 或者此项不进行设置，默认继承 Bucket 权限。 | NSString *            | 否   |
| grantRead         | 赋予被授权者读的权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| grantWrite        | 授予被授权者写的权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| grantFullControl  | 授予被授权者读写权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| versionID         | 指定源文件的 versionID，只有开启或开启后暂停的存储桶，才会响应此参数 | NSString *            | 否   |

#### 返回结果说明

通过 QCloudCopyObjectResult 返回请求结果。

| 参数名称     | 描述                                                         | 类型       |
| ------------ | ------------------------------------------------------------ | ---------- |
| eTag         | 返回文件的 MD5 算法校验值,ETag 的值可以用于检查 Object 的内容是否发生变化。 | NSString * |
| lastModified | 文件的最后修改时间，GMT格式                                  | NSString * |
| versionID    | 对象对应的 Version ID（在开启了版本控制的情况才有）           | NSString * |

#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 删除单个对象

#### 功能说明

删除单个对象（Delete Object）。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudDeleteObjectRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudDeleteObjectRequest，填入需要的参数。    
2. 调用 QCloudCOSXMLService 对象中的方法发出请求。    
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。   

#### QCloudDeleteObjectRequest 参数说明

| 参数名称 | 类型                                                         | 必填       | 描述 |
| -------- | ------------------------------------------------------------ | ---------- | ---- |
| object   | 对象键（Key）是对象在存储桶中的唯一标识。例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/pic.jpg`中，对象键为 doc/pic.jpg，详情请参见 [对象概述](https://intl.cloud.tencent.com/document/product/436/13324) | NSString * | 是   |
| bucket   | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; ，<br>例如 examplebucket-1250000000 | NSString * | 是   |

#### 示例


Objective-C 代码示例：


[//]: # (.cssg-snippet-objc-delete-object)
```objective-c
QCloudDeleteObjectRequest* deleteObjectRequest = [QCloudDeleteObjectRequest new];
deleteObjectRequest.bucket = @"examplebucket-1250000000";
deleteObjectRequest.object = @"exampleobject";

[deleteObjectRequest setFinishBlock:^(id outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML] DeleteObject:deleteObjectRequest];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-delete-object)
```swift
let deleteObject = QCloudDeleteObjectRequest.init();
deleteObject.bucket = "examplebucket-1250000000";
deleteObject.object = "exampleobject";
deleteObject.finishBlock = {(result,error)in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().deleteObject(deleteObject);
```

#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 删除多个对象

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudDeleteMultipleObjectRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudDeleteMultipleObjectRequest，填入需要的参数,将您想要删除的对象封装成一个QCloudDeleteObjectInfo对象,放入deleteObjects的objects数组中。    
2. 调用 QCloudCOSXMLService 对象中的方法发出请求。    
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。   

#### QCloudDeleteMultipleObjectRequest 请求参数说明

| 参数名称      | 描述                                                         | 类型               | 必填 |
| ------------- | ------------------------------------------------------------ | ------------------ | ---- |
| bucket        | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;，<br>例如 examplebucket-1250000000 | NSString *         | 是   |
| deleteObjects | 封装了需要批量删除的多个对象的信息                           | QCloudDeleteInfo * | 是   |

QCloudDeleteInfo参数说明

| 参数名称 | 描述                                                         | 类型                                      | 必填 |
| -------- | ------------------------------------------------------------ | ----------------------------------------- | ---- |
| objects  | 存放需要删除对象信息的数组                                   | NSArray&lt;QCloudDeleteObjectInfo `*` > * | 是   |
| Quiet    | 布尔值，这个值决定了是否启动 Quiet 模式：<br><li>true：启动 Quiet 模式<br><li>false：启动 Verbose 模式<br>默认值为 False | NSArray&lt;QCloudDeleteObjectInfo `*` > * | 否   |

CloudDeleteObjectInfo 参数说明

| 参数名称 | 描述                                                         | 类型       | 必填 |
| -------- | ------------------------------------------------------------ | ---------- | ---- |
| key      | 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/picture.jpg`中，对象键为 doc/picture.jpg，详情请参见 [对象概述](https://intl.cloud.tencent.com/document/product/436/13324) | NSString * | 是   |

#### 返回结果说明

QCloudDeleteResult 参数说明

| 参数名称       | 描述                                               | 类型                              |
| -------------- | -------------------------------------------------- | --------------------------------- |
| deletedObjects | 存放被删除对象信息的数组                           | NSArray<QCloudDeleteResultRow*> * |
| lastModified   | 文件的最后修改时间，GMT格式                        | NSString *                        |
| versionID      | 对象对应的 Version ID（在开启了版本控制的情况才有） | NSString *                        |

QCloudDeleteResultRow 参数说明

| 参数名称 | 描述            | 类型       |
| -------- | --------------- | ---------- |
| key      | 删除的对象的key | NSString * |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-delete-multi-object)
```objective-c
QCloudDeleteMultipleObjectRequest* delteRequest = [QCloudDeleteMultipleObjectRequest new];
delteRequest.bucket = @"examplebucket-1250000000";

QCloudDeleteObjectInfo* deletedObject0 = [QCloudDeleteObjectInfo new];
deletedObject0.key = @"exampleobject";

QCloudDeleteInfo* deleteInfo = [QCloudDeleteInfo new];
deleteInfo.quiet = NO;
deleteInfo.objects = @[deletedObject0];
delteRequest.deleteObjects = deleteInfo;

[delteRequest setFinishBlock:^(QCloudDeleteResult* outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML] DeleteMultipleObject:delteRequest];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-delete-multi-object)
```swift
let mutipleDel = QCloudDeleteMultipleObjectRequest.init();
mutipleDel.bucket = "examplebucket-1250000000";

let info1 = QCloudDeleteObjectInfo.init();
info1.key = "exampleobject";
let info2 = QCloudDeleteObjectInfo.init();


let deleteInfos = QCloudDeleteInfo.init();
deleteInfos.objects = [info1];
deleteInfos.quiet = false;
mutipleDel.deleteObjects = deleteInfos;

mutipleDel.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().deleteMultipleObject(mutipleDel);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的error里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

## 分块操作



- 分块上传对象： 初始化分块上传，  上传分块， 完成所有分块上传。
- 分块续传：查询已上传的分块， 上传分块，完成所有分块上传。
- 删除已上传分块。

### 查询分块上传

#### 功能说明

查询指定存储桶中正在进行的分块上传。

#### 方法原型

进行存储桶操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudListBucketMultipartUploadsRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudListBucketMultipartUploadsRequest，填入需要的参数，如返回结果的前缀、编码方式等。    
2. 调用 QCloudCOSXMLService 对象中的 ListBucketMultipartUploads 方法发出请求。    
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。   

#### QCloudListBucketMultipartUploadsRequest 参数说明

| 参数名称       | 描述                                                         | 类型       | 必填 |
| -------------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket         | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; ，<br>例如 examplebucket-1250000000 | NSString * | 是   |
| prefix         | 限定返回的 Object key 必须以 Prefix 作为前缀。注意使用 prefix 查询时，返回的 key 中仍会包含 Prefix | NSString * | 否   |
| delimiter      | 定界符为一个符号：<br><li>如果有 Prefix，则将 Prefix 到 delimiter 之间的相同路径归为一类，定义为 Common Prefix，然后列出所有 Common Prefix<br><li>如果没有 Prefix，则从路径起点开始。可以将其理解为结束的符号，例如如果想要结尾是 A 的结果，那么将 delimiter 设置为 A 即可 | NSString * | 否   |
| encodingType   | 规定返回值的编码方式，可选值：url                            | NSString * | 否   |
| keyMarker      | 列出条目从该 key 值开始                                      | NSString * | 否   |
| uploadIDMarker | 列出条目从该 UploadId 值开始                                 | int        | 否   |
| maxUploads     | 设置最大返回的  multipart 数量，合法值1 - 1000                | int        | 否   |

#### 返回结果说明

QCloudListMultipartUploadsResult 参数说明

| 参数名称     | 描述                                                         | 类型       |
| ------------ | ------------------------------------------------------------ | ---------- |
| bucket       | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; ，<br>例如 examplebucket-1250000000 | NSString * |
| prefix       | 限定返回的 Object key 必须以 Prefix 作为前缀。注意使用 prefix 查询时，返回的 key 中仍会包含 Prefix | NSString * |
| delimiter    | 定界符为一个符号：<br><li>如果有 Prefix，则将 Prefix 到 delimiter 之间的相同路径归为一类，定义为 Common Prefix，然后列出所有 Common Prefix<br><li>如果没有 Prefix，则从路径起点开始 | NSString * |
| encodingType | 规定返回值的编码方式，可选值：url                            | NSString * |
| keyMarker    | 列出条目从该 key 值开始                                      | NSString * |
| maxUploads   | 设置最大返回的 multipart 数量，合法值1 - 1000                 | int        |
| uploads      | 所有已经上传的分块信息                                       | NSArray*   |
| isTruncated  | 文件是否有被截断                                             | NSString * |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-list-multi-upload)
```objecitve-c
QCloudListBucketMultipartUploadsRequest* uploads = [QCloudListBucketMultipartUploadsRequest new];
uploads.bucket = @"examplebucket-1250000000";
uploads.maxUploads = 100;

[uploads setFinishBlock:^(QCloudListMultipartUploadsResult* result, NSError *error) {
    //可以从 result 中返回分块信息
}];

[[QCloudCOSXMLService defaultCOSXML] ListBucketMultipartUploads:uploads];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-list-multi-upload)
```swift
let listParts = QCloudListBucketMultipartUploadsRequest.init();
listParts.bucket = "examplebucket-1250000000";
listParts.maxUploads = 100;
listParts.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().listBucketMultipartUploads(listParts);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。返回错误码（封装在返回的error里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码由苹果公司定义，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。


### <span id = "INIT_MULIT_UPLOAD"> 初始化分块上传 </span>

#### 功能说明

初始化分块上传，获取对应的 uploadId（Initiate Multipart Upload）。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudInitiateMultipartUploadRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudInitiateMultipartUploadRequest，填入需要的参数。    
2. 调用 QCloudCOSXMLService 对象中的 InitiateMultipartUpload 方法发出请求。    
3. 从回调的 finishBlock 中的获取具体内容。   

#### 参数说明

| 参数名称           | 描述                                                         | 类型                  | 必填 |
| ------------------ | ------------------------------------------------------------ | --------------------- | ---- |
| Object             | 上传文件（对象）的文件名，也是对象的key。 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/picture.jpg`中，对象键为 doc/picture.jpg，详细描述请参见 [对象描述](https://intl.cloud.tencent.com/document/product/436/13324)。 | NSString *            | 是   |
| bucket             | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt; ,<br>例如 examplebucket-1250000000 | NSString *            | 是   |
| cacheControl       | RFC 2616中定义的缓存策略                                     | NSString *            | 否   |
| contentDisposition | RFC 2616中定义的文件名称                                     | NSString *            | 否   |
| expect             | 当使用`expect=@"100-continue" `时，在收到服务端确认后才会发送请求内容 | NSString *            | 否   |
| expires            | RFC 2616 中定义的过期时间                                    | NSString *            | 否   |
| storageClass       | 对象的存储级别，枚举值：<br><li>STANDARD(QCloudCOSStorageStandard)<br><li>STANDARD_IA(QCloudCOSStorageStandardIA)<br><li>ARCHIVE(QCloudCOSStorageARCHIVE)<br>默认值：STANDARD(QCloudCOSStorageStandard) | QCloudCOSStorageClass | 否   |
| accessControlList  | 定义 Object 的 ACL 属性，有效值：private，public-read，default。默认值：default（继承 Bucket 权限）。<br>注意：当前访问策略条目限制为1000条，如果您不需要进行 Object ACL 控制，请填 default 或者此项不进行设置，默认继承 Bucket 权限 | NSString *            | 否   |
| grantRead          | 赋予被授权者读的权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| grantWrite         | 授予被授权者写的权限，格式：id="OwnerUin"                    | NSString *            | 否   |
| grantFullControl   | 授予被授权者读写权限，格式：id="OwnerUin"                    | NSString *            | 否   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-init-multi-upload)
```objective-c
QCloudInitiateMultipartUploadRequest* initrequest = [QCloudInitiateMultipartUploadRequest new];
initrequest.bucket = @"examplebucket-1250000000";
initrequest.object = @"exampleobject";

[initrequest setFinishBlock:^(QCloudInitiateMultipartUploadResult* outputObject, NSError *error) {
    //获取分块上传的 uploadId，后续的上传都需要这个 ID，请保存以备后续使用
    @"exampleUploadId" = outputObject.uploadId;
}];

[[QCloudCOSXMLService defaultCOSXML] InitiateMultipartUpload:initrequest];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-init-multi-upload)
```swift
let initRequest = QCloudInitiateMultipartUploadRequest.init();
initRequest.bucket = "examplebucket-1250000000";
initRequest.object = "exampleobject";
initRequest.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        //获取分块上传的 uploadId，后续的上传都需要这个 ID，请保存以备后续使用
        self.uploadId = result!.uploadId;
        print(result!.uploadId);
    }}
QCloudCOSXMLService.defaultCOSXML().initiateMultipartUpload(initRequest);
```

#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 上传分块

#### 功能说明

分块上传文件。

#### QCloudUploadPartRequest 参数说明

| 参数名称    | 描述                                                         | 类型       | 必填 |
| ----------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket      | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为&lt;BucketName-APPID&gt; ，<br>例如 examplebucket-1250000000 | NSString * | 是   |
| object      | 对象名称                                                   | NSString * | 是   |
| uploadId    | 标识本次分块上传的 ID。<br>使用 Initiate Multipart Upload 接口初始化分块上传时会得到一个 uploadId，该 ID 不但唯一标识这一分块数据，也标识了这分块数据在整个文件内的相对位置 | NSString * | 是   |
| partNumber  | 标识本次分块上传的编号                                       | int        | 是   |
| contentSHA1 | 本次分块上传的 sha1 值                                       | NSString * | 是   |
| body        | 上传的数据：支持`NSData*`，`NSURL`（本地 URL）和`QCloudFileOffsetBody*`三种类型 | BodyType   | 是   |

#### 返回结果说明

QCloudUploadPartResult 参数说明

| 参数名称 | 描述        | 类型       |
| -------- | ----------- | ---------- |
| eTag     | 文件的 etag | NSString * |

#### 方法原型

COS iOS SDK 中分块上传请求的方法具体步骤如下：

1. 实例化 QCloudUploadPartRequest，填入需要的参数。
2. 调用 QCloudCOSXMLService 对象中的 UploadPart 方法发出请求。
3. 从回调的 finishBlock 中的 QCloudUploadPartResult 获取具体内容。

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-upload-part)
```objective-c
QCloudUploadPartRequest* request = [QCloudUploadPartRequest new];
request.bucket = @"examplebucket-1250000000";
request.object = @"exampleobject";
request.partNumber = 1;
//标识本次分块上传的 ID；使用 Initiate Multipart Upload 接口初始化分块上传时会得到一个 uploadId
//该 ID 不但唯一标识这一分块数据，也标识了这分块数据在整个文件内的相对位置
request.uploadId = @"exampleUploadId";
//上传的数据：支持 NSData*，NSURL(本地 URL) 和 QCloudFileOffsetBody * 三种类型
request.body = [@"testFileContent" dataUsingEncoding:NSUTF8StringEncoding];

[request setSendProcessBlock:^(int64_t bytesSent,
                               int64_t totalBytesSent,
                               int64_t totalBytesExpectedToSend) {
    //上传进度信息
}];
[request setFinishBlock:^(QCloudUploadPartResult* outputObject, NSError *error) {
    QCloudMultipartInfo *part = [QCloudMultipartInfo new];
    //获取所上传分块的 etag
    part.eTag = outputObject.eTag;
    part.partNumber = @"1";
    // 保存起来用于最好完成上传时使用
    self.parts = @[part];
}];

[[QCloudCOSXMLService defaultCOSXML]  UploadPart:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-upload-part)
```swift

let uploadPart = QCloudUploadPartRequest<AnyObject>.init();
uploadPart.bucket = "examplebucket-1250000000";
uploadPart.object = "exampleobject";
uploadPart.partNumber = 1;
//标识本次分块上传的 ID；使用 Initiate Multipart Upload 接口初始化分块上传时会得到一个 uploadId
//该 ID 不但唯一标识这一分块数据，也标识了这分块数据在整个文件内的相对位置
uploadPart.uploadId = "exampleUploadId";
if self.uploadId != nil {
     uploadPart.uploadId = self.uploadId!;
}

let dataBody:NSData? = "wrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrwwrwrwrwrwrw".data(using: .utf8) as NSData?;
uploadPart.body = dataBody!;
uploadPart.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        let mutipartInfo = QCloudMultipartInfo.init();
        //获取所上传分块的 etag
        mutipartInfo.eTag = result!.eTag;
        mutipartInfo.partNumber = "1";
        // 保存起来用于最好完成上传时使用
        self.parts = [mutipartInfo];
    }}
uploadPart.sendProcessBlock = {(bytesSent,totalBytesSent,totalBytesExpectedToSend) in
    //上传进度信息
    print("totalBytesSent:\(totalBytesSent) totalBytesExpectedToSend:\(totalBytesExpectedToSend)");
    
}
QCloudCOSXMLService.defaultCOSXML().uploadPart(uploadPart);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 复制分块

#### 功能说明

分块复制文件。

#### QCloudUploadPartRequest 参数说明

| 参数名称   | 描述                                                         | 类型       | 必填 |
| ---------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket     | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;。<br>例如 examplebucket-1250000000 | NSString * | 是   |
| object     | 对象名称                                                   | NSString * | 是   |
| uploadId   | 标识本次分块上传的 ID。<br>使用 Initiate Multipart Upload 接口初始化分块上传时会得到一个 uploadId，该 ID 不但唯一标识这一分块数据，也标识了这分块数据在整个文件内的相对位置 | NSString * | 是   |
| partNumber | 标识本次分块上传的编号                                       | int        | 是   |
| source     | 源文件 URL 路径，可以通过 versionid 子资源指定历史版本       | NSString * | 是   |
| body       | 上传的数据：支持`NSData*`，`NSURL`（本地 URL）和`QCloudFileOffsetBody*`三种类型 | BodyType   | 是   |

#### 返回结果说明

QCloudUploadPartResult 参数说明

| 参数名称 | 描述        | 类型       |
| -------- | ----------- | ---------- |
| eTag     | 文件的 etag | NSString * |

#### 方法原型

COS iOS SDK 中分块上传请求的方法具体步骤如下：

1. 实例化 QCloudUploadPartCopyRequest，填入需要的参数。
2. 调用 QCloudCOSXMLService 对象中的 UploadPartCopy 方法发出请求。
3. 从回调的 finishBlock 中的 QCloudCopyObjectResult 获取具体内容。

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-upload-part-copy)
```objective-c
QCloudUploadPartCopyRequest* request = [[QCloudUploadPartCopyRequest alloc] init];
request.bucket = @"examplebucket-1250000000";
request.object = @"exampleobject";
//源文件 URL 路径，可以通过 versionid 子资源指定历史版本
request.source = @"sourcebucket-1250000000.cos.COS_REGION.myqcloud.com/sourceObject";
//在初始化分块上传的响应中，会返回一个唯一的描述符（upload ID）
request.uploadID = @"exampleUploadId";
request.partNumber = 1; // 标志当前分块的序号

[request setFinishBlock:^(QCloudCopyObjectResult* result, NSError* error) {
    QCloudMultipartInfo *part = [QCloudMultipartInfo new];
    //获取所复制分块的 etag
    part.eTag = result.eTag;
    part.partNumber = @"1";
    // 保存起来用于最后完成上传时使用
    self.parts=@[part];
}];

[[QCloudCOSXMLService defaultCOSXML]UploadPartCopy:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-upload-part-copy)
```swift
let req = QCloudUploadPartCopyRequest.init();
req.bucket = "examplebucket-1250000000";
req.object = "exampleobject";
//源文件 URL 路径，可以通过 versionid 子资源指定历史版本
req.source = "sourcebucket-1250000000.cos.COS_REGION.myqcloud.com/sourceObject";
//在初始化分块上传的响应中，会返回一个唯一的描述符（upload ID）
req.uploadID = "exampleUploadId";
if self.uploadId != nil {
    req.uploadID = self.uploadId!;
}

//标志当前分块的序号
req.partNumber = 1;
req.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        let mutipartInfo = QCloudMultipartInfo.init();
        //获取所复制分块的 etag
        mutipartInfo.eTag = result!.eTag;
        mutipartInfo.partNumber = "1";
        self.parts = [mutipartInfo];
    }}
QCloudCOSXMLService.defaultCOSXML().uploadPartCopy(req);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### <span id = "LIST_MULIT_UPLOAD"> 查询已上传块 </span>

#### 功能说明

查询指定 uploadId 已上传的分块（List Parts）。

#### QCloudListMultipartRequest 请求参数说明

| 参数名称         | 描述                                                         | 类型       | 必填 |
| ---------------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket           | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;。<br>例如 examplebucket-1250000000 | NSString * | 是   |
| object           | 对象的名称                                                   | NSString * | 是   |
| uploadId         | 本次要查询的分块上传的uploadId                               | NSString * | 是   |
| maxPartsCount    | 单次返回最大的条目数量，默认1000                             | NSString * | 否   |
| partNumberMarker | 默认以 UTF-8 二进制顺序列出条目，所有列出条目从 marker 开始  | NSString * | 否   |
| encodingType     | 规定返回值的编码方式                                         | int        | 否   |

#### 返回结果说明

 QCloudListPartsResult 参数

| 参数名称         | 描述                                                         | 类型                       |
| ---------------- | ------------------------------------------------------------ | -------------------------- |
| bucket           | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;。<br>例如 examplebucket-1250000000 | NSString *                 |
| encodingType     | 限规定返回值的编码格式                                       | NSString *                 |
| key              | 对象的名称                                                   | NSString *                 |
| uploadId         | 本次查询的分块上传的 uploadId                                | NSString *                 |
| storageClass     | 用来表示这些分块的存储级别                                   | QCloudCOSStorageClass      |
| partNumberMarker | 默认以 UTF-8 二进制顺序列出条目，所有列出条目从 marker 开始  | int                        |
| nextNumberMarker | 假如返回条目被截断，则返回 NextMarker 就是下一个条目的起点   | NSString *                 |
| maxParts         | 单次返回的最大条目数                                         | QCloudCOSStorageClass      |
| isTruncated      | 返回条目是否被截断                                           | BOOL                       |
| initiator        | 用来标识本次上传发起者的信息                                 | NSString *                 |
| owner            | 用来标识这些分块所有者的信息                                 | QCloudCOSStorageClass      |
| parts            | 用来表示每一个块的信息                                       | QCloudMultipartUploadPart* |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-list-parts)
```objective-c
QCloudListMultipartRequest* request = [QCloudListMultipartRequest new];
request.object = @"exampleobject";
request.bucket = @"examplebucket-1250000000";
//在初始化分块上传的响应中，会返回一个唯一的描述符（upload ID）
request.uploadId = @"exampleUploadId";

[request setFinishBlock:^(QCloudListPartsResult * _Nonnull result, NSError * _Nonnull error) {
    //从 result 中获取已上传分块信息
}];

[[QCloudCOSXMLService defaultCOSXML] ListMultipart:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-list-parts)
```swift
let req = QCloudListMultipartRequest.init();
req.object = "exampleobject";
req.bucket = "examplebucket-1250000000";
//在初始化分块上传的响应中，会返回一个唯一的描述符（upload ID）
req.uploadId = "exampleUploadId";
if self.uploadId != nil {
    req.uploadId = self.uploadId!;
}
req.setFinish { (result, error) in
    if error != nil{
        print(error!);
    }else{
        //从 result 中获取已上传分块信息
        print(result!);
    }}

QCloudCOSXMLService.defaultCOSXML().listMultipart(req);
```

#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### <span id = "COMPLETE_MULIT_UPLOAD"> 完成分块上传 </span>

#### 功能说明

实现完成整个分块上传（Complete Multipart Upload）。

#### QCloudCompleteMultipartUploadRequest 参数说明

| 参数名称 | 描述                                                         | 类型                                | 必填 |
| -------- | ------------------------------------------------------------ | ----------------------------------- | ---- |
| bucket   | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为&lt;BucketName-APPID&gt;。<br>例如 examplebucket-1250000000 | NSString *                          | 是   |
| object   | 对象的名称                                                   | NSString *                          | 是   |
| uploadId | 标识本次分块上传的 ID。<br>使用 Initiate Multipart Upload 接口初始化分块上传时会得到一个 uploadId，该 ID 不但唯一标识这一分块数据，也标识了这分块数据在整个文件内的相对位置 | NSString *                          | 是   |
| parts    | 完成分块上传的信息                                           | QCloudCompleteMultipartUploadInfo * | 是   |

#### 返回结果说明

 QCloudUploadObjectResult 参数说明

| 参数名称  | 描述                                                         | 类型       |
| --------- | ------------------------------------------------------------ | ---------- |
| location  | 创建对象的外网访问域名                                       | NSString * |
| bucket    | 分块上传的目标存储桶名： 可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;。<br>例如 examplebucket-1250000000 | NSString * |
| key       | 对象名                                                       | NSString * |
| eTag      | 合并后文件的 MD5 算法校验值                                  | NSString * |
| versionID | 对象对应的 Version ID（在开启版本控制的情况才有）          | NSString * |

#### 方法原型

COS iOS SDK 中完成整个分块上传请求的方法具体步骤如下：

1. 实例化 QCloudCompleteMultipartUploadRequest，填入需要的参数。
2. 调用 QCloudCOSXMLService 对象中的 CompleteMultipartUpload 方法发出请求。
3. 从回调的 finishBlock 中的 QCloudUploadObjectResult 获取具体内容。

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-complete-multi-upload)
```objective-c
QCloudCompleteMultipartUploadRequest *completeRequst = [QCloudCompleteMultipartUploadRequest new];
completeRequst.object = @"exampleobject";
completeRequst.bucket = @"examplebucket-1250000000";
//本次要查询的分块上传的 uploadId，可从初始化分块上传的请求结果 QCloudInitiateMultipartUploadResult 中得到
completeRequst.uploadId = @"exampleUploadId";
//已上传分块的信息
QCloudCompleteMultipartUploadInfo *partInfo = [QCloudCompleteMultipartUploadInfo new];
partInfo.parts = self.parts;
completeRequst.parts = partInfo;

[completeRequst setFinishBlock:^(QCloudUploadObjectResult * _Nonnull result, NSError * _Nonnull error) {
    //从 result 中获取上传结果
}];

[[QCloudCOSXMLService defaultCOSXML] CompleteMultipartUpload:completeRequst];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-complete-multi-upload)
```swift
let  complete = QCloudCompleteMultipartUploadRequest.init();
complete.bucket = "examplebucket-1250000000";
complete.object = "exampleobject";
//本次要查询的分块上传的 uploadId，可从初始化分块上传的请求结果 QCloudInitiateMultipartUploadResult 中得到
complete.uploadId = "exampleUploadId";
if self.uploadId != nil {
    complete.uploadId = self.uploadId!;
}


//已上传分块的信息
let completeInfo = QCloudCompleteMultipartUploadInfo.init();
if self.parts == nil {
    print("没有要完成的分块");
    return;
}

completeInfo.parts = self.parts!;
complete.parts = completeInfo;
complete.setFinish { (result, error) in
    if error != nil{
        print(error!)
    }else{
        //从 result 中获取上传结果
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().completeMultipartUpload(complete);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### <span id = "ABORT_MULIT_UPLOAD"> 终止分块上传 </span>

#### 功能说明

舍弃一个分块上传并删除已上传的块（Abort Multipart Upload）。

#### QCloudAbortMultipfartUploadRequest 参数说明

| 参数名称 | 描述                                                         | 类型       | 必填 |
| -------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket   | 存储桶名称，可在 [COS  控制台](https://console.cloud.tencent.com/cos5/bucket) 查看，命名格式：BucketName-APPID。<br>例如 examplebucket-1250000000 | NSString * | 是   |
| object   | 对象的名称                                                   | NSString * | 是   |
| uploadId | 要丢弃的分块上传的 ID。<br>使用 Initiate Multipart Upload 接口初始化分块上传时会得到一个 uploadId，该 ID 不但唯一标识这一分块数据，也标识了这分块数据在整个文件内的相对位置 | NSString * | 是   |

#### 方法原型

COS iOS SDK 中舍弃一个分块上传，且删除已上传分块请求的方法具体步骤如下：

1. 实例化 QCloudAbortMultipfartUploadRequest，填入需要的参数。
2. 调用 QCloudCOSXMLService 对象中的 AbortMultipfartUpload 方法发出请求。
3. 从回调的 finishBlock 中的 outputObject 获取具体内容。

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-abort-multi-upload)
```objective-c
QCloudAbortMultipfartUploadRequest *abortRequest = [QCloudAbortMultipfartUploadRequest new];
abortRequest.object = @"exampleobject";
abortRequest.bucket = @"examplebucket-1250000000";
//本次要查询的分块上传的 uploadId，可从初始化分块上传的请求结果 QCloudInitiateMultipartUploadResult 中得到
abortRequest.uploadId = @"exampleUploadId";

[abortRequest setFinishBlock:^(id outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML]AbortMultipfartUpload:abortRequest];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-abort-multi-upload)
```swift
let abort = QCloudAbortMultipfartUploadRequest.init();
abort.bucket = "examplebucket-1250000000";
abort.object = "exampleobject";
//本次要查询的分块上传的 uploadId，可从初始化分块上传的请求结果 QCloudInitiateMultipartUploadResult 中得到
abort.uploadId = "exampleUploadId";
if self.uploadId != nil {
    abort.uploadId = self.uploadId!;
}

abort.finishBlock = {(result,error)in
    if error != nil{
        print(error!)
    }else{
        //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
        print(result!);
    }    
}
QCloudCOSXMLService.defaultCOSXML().abortMultipfartUpload(abort);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

## 其他操作

### 恢复归档对象

#### 功能说明

恢复归档对象（POST Object restore）。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudPostObjectRestoreRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudPostObjectRestoreRequest，填入需要设置的对象名、存储桶名。    
2. 调用 QCloudCOSXMLService 对象中的方法发出请求。    
3. 从回调的 finishBlock 中的获取具体内容。   

#### QCloudPostObjectRestoreRequest 请求参数说明

| 参数名称       | 描述                                                         | 类型                   | 必填 |
| -------------- | ------------------------------------------------------------ | ---------------------- | ---- |
| object         | 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/picture.jpg` 中，对象键为 doc/picture.jpg，更详细的描述可以参见 [对象描述](https://intl.cloud.tencent.com/document/product/436/13324)。 | NSString *             | 是   |
| bucket         | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为&lt;BucketName-APPID&gt; ，例如 examplebucket-1250000000 | NSString *             | 是   |
| restoreRequest | 模恢复数据的配置信息                                         | QCloudRestoreRequest * | 是   |

QCloudRestoreRequest 参数说明

| 参数名称         | 描述                   | 类型               | 必填 |
| ---------------- | ---------------------- | ------------------ | ---- |
| days             | 设置临时副本的过期时间 | int64_t            | 是   |
| CASJobParameters | 复原的过程类型配置信息 | CASJobParameters * | 是   |

CASJobParameters 参数说明

| 参数名称 | 描述                                                         | 类型          | 必填 |
| -------- | ------------------------------------------------------------ | ------------- | ---- |
| tier     | 恢复模式，支持的三种恢复模式，分别为：<br><li>Standard（标准模式，恢复任务在3 - 5小时内完成）<br><li>Expedited（极速模式，恢复任务在15分钟内可完成）<br><li>Bulk（批量模式，恢复任务在5 - 12小时内完成） | QCloudCASTier | 是   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-restore-object)
```objective-c
QCloudPostObjectRestoreRequest *req = [QCloudPostObjectRestoreRequest new];
req.bucket = @"examplebucket-1250000000";
req.object = @"exampleobject";
req.restoreRequest.days  = 10;
req.restoreRequest.CASJobParameters.tier =QCloudCASTierStandard;

[req setFinishBlock:^(id outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML] PostObjectRestore:req];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-restore-object)
```swift
let restore = QCloudPostObjectRestoreRequest.init();
restore.bucket = "examplebucket-1250000000";
restore.object = "exampleobject";
restore.restoreRequest.days = 10;
restore.restoreRequest.casJobParameters.tier = .standard;
restore.finishBlock = {(result,error)in
    if error != nil{
        print(error!)
    }else{
        //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().postObjectRestore(restore);
```

#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 设置对象 ACL

#### 功能说明

设置对象访问权限控制列表（ACL）。

#### 方法原型

进行对象操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudPutObjectACLRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudPutObjectACLRequest，填入存储桶名，和一些额外需要的参数，如授权的具体信息等。    
2. 调用 QCloudCOSXMLService 对象中的方法发出请求。    
3. 从回调的 finishBlock 中获取设置的完成情况，若 error 为空，则设置成功。   

#### QCloudPutObjectACLRequest 参数说明

| 参数名称          | 描述                                                         | 类型       | 必填 |
| ----------------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket            | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为 &lt;BucketName-APPID&gt;，<br>例如 examplebucket-1250000000 | NSString * | 是   |
| object            | 对象名称                                                     | NSString * | 是   |
| accessControlList | 定义 Object 的 ACL 属性，有效值：private，public-read，default。默认值：default（继承 Bucket 权限）。<br>注意：当前访问策略条目限制为1000条，如果您不需要进行 Object ACL 控制，请填 default 或者此项不进行设置，默认继承 Bucket 权限 | NSString * | 否   |
| grantRead         | 赋予被授权者读的权限。格式：id="OwnerUin"                    | NSString * | 否   |
| grantWrite        | 授予被授权者写的权限。格式：id="OwnerUin"                    | NSString * | 否   |
| grantFullControl  | 授予被授权者读写权限。格式：id="OwnerUin"                    | NSString * | 否   |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-put-object-acl)
```objective-c
QCloudPutObjectACLRequest* request = [QCloudPutObjectACLRequest new];
request.object = @"exampleobject";
request.bucket = @"examplebucket-1250000000";
NSString *grantString = [NSString stringWithFormat:@"id=\"%@\"",@"1250000000"];
request.grantFullControl = grantString;
[request setFinishBlock:^(id outputObject, NSError *error) {
    //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
}];

[[QCloudCOSXMLService defaultCOSXML] PutObjectACL:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-put-object-acl)
```swift
let putObjectACl = QCloudPutObjectACLRequest.init();
putObjectACl.bucket = "examplebucket-1250000000";
putObjectACl.object = "exampleobject";
let grantString = "id=\"1250000000\"";
putObjectACl.grantFullControl = grantString;
putObjectACl.finishBlock = {(result,error)in
    if error != nil{
        print(error!)
    }else{
        //可以从 outputObject 中获取 response 中 etag 或者自定义头部等信息
        print(result!);
    }}
QCloudCOSXMLService.defaultCOSXML().putObjectACL(putObjectACl);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

### 查询对象 ACL

#### 功能说明

查询对象访问权限控制列表（ACL）。

#### 方法原型

进行文件操作之前，需要导入头文件 QCloudCOSXML/QCloudCOSXML.h。在此之前您需要完成 [初始化操作](https://intl.cloud.tencent.com/document/product/436/8629#step1)。先生成一个 QCloudGetObjectACLRequest 实例，然后填入一些需要的额外限制条件，通过并获得内容。具体步骤如下：    

1. 实例化 QCloudGetObjectACLRequest，填入存储桶的名称，和需要查询对象的名称。    
2. 调用 QCloudCOSXMLService 对象中的 GetObjectACL 方法发出请求。    
3. 从回调的 finishBlock 中的 QCloudACLPolicy 对象中查询封装好的 ACL 的具体信息。   

#### QCloudGetObjectACLRequest 参数说明

| 参数名称  | 描述                                                         | 类型       | 必填 |
| --------- | ------------------------------------------------------------ | ---------- | ---- |
| bucket    | 存储桶名，可在 [COS 控制台 ](https://console.cloud.tencent.com/cos5/bucket) 查看，格式为&lt;BucketName-APPID&gt; ，例如 examplebucket-1250000000 | NSString * | 是   |
| object    | 对象键（Key）是对象在存储桶中的唯一标识。<br>例如，在对象的访问域名`examplebucket-1250000000.cos.ap-guangzhou.myqcloud.com/doc/pic.jpg` 中，对象键为 doc/pic.jpg，更详细的描述可以参见 [对象描述](https://intl.cloud.tencent.com/document/product/436/13324)。 | NSString * | 是   |
| versionID | 指定版本控制中的 Version ID。                                  | NSString * | 是   |

#### 返回结果说明

QCloudACLPolicy 参数说明

| 参数名称          | 描述                   | 类型                      |
| ----------------- | ---------------------- | ------------------------- |
| owner             | 持有者的信息           | QCloudACLOwner *          |
| accessControlList | 分被授权者与权限的信息 | QCloudAccessControlList * |

QCloudACLOwner 参数说明

| 参数名称    | 描述                | 类型       |
| ----------- | ------------------- | ---------- |
| displayName | Bucket 持有者的名称 | NSString * |
| identifier  | Bucket 持有者 ID    | NSString * |

QCloudAccessControlList 参数说明

| 参数名称  | 描述                   | 类型                       |
| --------- | ---------------------- | -------------------------- |
| ACLGrants | 存放被授权者信息的数组 | NSArray<QCloudACLGrant*> * |

QCloudACLGrant 参数说明

| 参数名称   | 描述                                                         | 类型                |
| ---------- | ------------------------------------------------------------ | ------------------- |
| grantee    | 说明被授权者的信息。type 类型可以为 RootAccount，Subaccount。<br><li>当 type 类型为 RootAccount 时，ID 中指定的是主帐号<br><li>当 type 类型为 Subaccount 时，ID 中指定的是子帐号 | QCloudACLGrantee *  |
| permission | 指明授予被授权者的权限信息，枚举值：READ，FULL_CONTROL       | QCloudCOSPermission |

#### 示例


Objective-C 代码示例：

[//]: # (.cssg-snippet-objc-get-object-acl)
```objective-c
QCloudGetObjectACLRequest *request = [QCloudGetObjectACLRequest new];
request.object = @"exampleobject";
request.bucket = @"examplebucket-1250000000";
__block QCloudACLPolicy* policy;
[request setFinishBlock:^(QCloudACLPolicy * _Nonnull result, NSError * _Nonnull error) {
    policy = result;
}];

[[QCloudCOSXMLService defaultCOSXML] GetObjectACL:request];
```

Swift 代码示例：

[//]: # (.cssg-snippet-swift-get-object-acl)
```swift
let getObjectACL = QCloudGetObjectACLRequest.init();
getObjectACL.bucket = "examplebucket-1250000000";
getObjectACL.object = "exampleobject";
getObjectACL.setFinish { (result, error) in
    if error != nil{
        print(error!)
    }else{
        //可以从 result 的 accessControlList 中获取对象的 ACL
        print(result!.accessControlList);
    }}
QCloudCOSXMLService.defaultCOSXML().getObjectACL(getObjectACL);
```
#### 返回错误码说明

当 SDK 请求失败的时候，返回的 error 将不为空，并且包括了错误码、错误描述和其它一些调试必备的信息，以帮助开发者快速解决问题。

返回错误码（封装在返回的 error 里）主要包括两类：设备本身因为网络原因等返回的错误码，以及 COS 返回的错误码。

- 对于设备本身因为网络原因产生的错误码，都是负数并且是四位数，例如-1001，这类错误码是苹果定义的，可以参见 Foundation 框架中的 NSURLError.h 头文件内的定义，或者是 [苹果官方文档说明](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes)。
- 对于 COS 返回的错误码，是基于 HTTP 的状态码而来的，也就是404，503这类。对于这类错误码，可以参见 [错误码]( https://cloud.tencent.com/document/product/436/7730) 文档寻求解决方案。
- 对于 SDK 自定义的错误码，均为5位数且都是正数，如10000、20000等。对于这类错误码，可以参见 [SDK 错误码](https://intl.cloud.tencent.com/document/product/436/30610) 文档寻求解决方案。

## 加入自定义头部

如果有需要加入自定义头部的需求，可以使用自定义头部。支持加入自定义头部的类，都会有 customHeaders 这个属性：

```objective-c
@property (strong, nonatomic) NSMutableDictionary* customHeaders;
```

对于所有该属性内的键值对，都会以对应的形式加入到构建请求的 HTTP 头部中。

## 服务器加密

如果需要对上传的对象进行加密，我们支持以下加密方式。

#### 使用 COS 托管加密密钥的服务端加密（SSE-COS）保护数据

COS 会帮助您在数据写入数据中心时自动加密，并在您取用该数据时自动解密。目前支持使用 COS 主密钥对数据进行 AES-256 加密。

iOS SDK 通过调用 -(void)setCOSServerSideEncyption 方法来完成。

Objective-C 代码示例：
```objective-c
[request setCOSServerSideEncyption];
```

Swift 代码示例：
```swift
request.setCOSServerSideEncyption();
```

#### 使用客户提供的加密密钥的服务器端加密 （SSE-C）保护数据

iOS SDK 通过调用 -(void)setCOSServerSideEncyptionWithCustomerKey:(NSString \*)customerKey 方法来完成。

>
>- 该加密所运行的服务需要使用 HTTPS 请求。
>- customerKey：用户提供的密钥，传入一个32字节的字符串，支持数字、字母、字符的组合，不支持中文。
>- 如果上传的源文件调用了该方法，那么在使用 QCloudCOSXMLDownloadObjectRequest（下载）、QCloudHeadObjectRequest（查询）、QCloudCOSXMLUploadObjectRequest（上传）、QCloudCOSXMLUploadObjectRequest（copy）对源对象操作的时候也要调用该方法。

Objective-C 代码示例：
```objective-c
NSString *customKey = @"123456qwertyuioplkjhgfdsazxcvbnm";
[put setCOSServerSideEncyptionWithCustomerKey:customKey];
```

Swift 代码示例

```swift
let customKey = "123456qwertyuioplkjhgfdsazxcvbnm";
putObject.setCOSServerSideEncyptionWithCustomerKey(customKey);
```
