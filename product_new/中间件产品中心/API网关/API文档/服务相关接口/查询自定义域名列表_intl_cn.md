## 接口描述

本接口（DescribeServiceSubDomains）用于查询自定义列表。
API 网关可绑定自定义域名到服务，用于服务调用。此接口用于查询用户绑定在服务的自定义域名列表。


## 输入参数

以下请求参数列表仅列出了接口请求参数，其它参数可参考 [公共请求参数](https://intl.cloud.tencent.com/document/product/628/18814)。

| 参数名称      | 是否必选 | 类型     | 描述      |
| --------- | ---- | ------ | ------- |
| serviceId | 是    | String | 服务唯一 ID |

## 输出参数

| 参数名称   | 类型          | 描述                                                         |
| ---------- | ------------- | ------------------------------------------------------------ |
| code       | Int           | 公共错误码，0表示成功，其他值表示失败。详见错误码页面的 [公共错误码](https://intl.cloud.tencent.com/document/product/628/18822) |
| codeDesc   | String        | 业务侧错误码。成功时返回 Success，错误时返回具体业务错误原因 |
| totalCount | Int           | 服务下的自定义域名数量                                       |
| domainSet  | List of Array | 域名列表                                                     |

其中 domainSet 是域名列表，它由 domainStatus 构成，domainStatus 的组成如下：

| 参数名称          | 类型     | 描述                             |
| ------------- | ------ | ------------------------------ |
| domainName    | String | 域名名称                         |
| status        | String | 域名解析状态。True 表示正常解析，Flase 表示解析失败 |
| certificateId | String | 证书 ID                          |


## 示例 
```
https://apigateway.api.qcloud.com/v2/index.php?
&<公共请求参数>
&Action=DescribeSubDomain
&serviceId=service-XXXX
```
返回示例如下：
```
{
	"code": "0",
	"message": "",
	"codeDesc": "Success",
	"totalCount": 2,
	"domainSet": [{
			"domainName": "domain1",
			"status": "True",
			"certificateId": "XXXX"
		},
		{
			"domainName": "domain2",
			"status": "Flase",
			"certificateId": "XXXX"
		}
	]
}
```




