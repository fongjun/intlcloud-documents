## 接口描述

本接口（DescribeUsagePlansStatus）用于查询一个或多个使用计划列表。可查询到这些使用计划的名称、描述、QPS 等信息。

## 输入参数

以下请求参数列表仅列出了接口请求参数，其它参数可参考 [公共请求参数](https://intl.cloud.tencent.com/document/product/628/18814)。

| 参数名称           | 是否必选 | 类型               | 描述                  |
| -------------- | ---- | ---------------- | ------------------- |
| usagePlanIds.n | 否    | Array of Strings | 按照一个或者多个使用计划唯一 ID 查询。 |
| offset         | 否    | Int              | 偏移量，默认为 0。           |
| limit          | 否    | Int              | 返回数量，默认为 20，最大值为 100。 |
| orderby        | 否    | String           | 根据哪个字段排序。           |
| order          | 否    | String           | 排序方式。               |
| searchName     | 否    | String           | 按照使用计划名字模糊搜索。       |
| searchId       | 否    | String           | 按照使用计划唯一 ID 精确搜索。     |

## 输出参数

| 参数名称           | 类型          | 描述                                                         |
| ------------------ | ------------- | ------------------------------------------------------------ |
| code               | Int           | 公共错误码, 0 表示成功，其他值表示失败。详见错误码页面的 [公共错误码](https://intl.cloud.tencent.com/document/product/628/18822)。 |
| codeDesc           | String        | 业务侧错误码。成功时返回 Success，错误时返回具体业务错误原因。 |
| message            | String        | 模块错误信息描述，与接口相关。                               |
| totalCount         | Int           | 符合条件的使用计划数量。                                     |
| usagePlanStatusSet | List of Array | 使用计划列表。                                               |

其中，usagePlanStatusSet 是 usagePlanStatus 构成的数组，usagePlanStatus 的构成如下：

| 参数名称                 | 类型        | 描述                                       |
| -------------------- | --------- | ---------------------------------------- |
| usagePlanId          | String    | 使用计划唯一 ID。                                |
| usagePlanName        | String    | 用户自定义的使用计划名称。                            |
| usagePlanDescription | String    | 用户自定义的使用计划描述。                            |
| requestControlUnit   | String    | 请求限制单位，默认是 SECOND。当前只能是 SECOND,后续会有 MINUTE、HOUR 等。 |
| requestControlNum    | Int       | 请求限制数。                                   |
| createdTime          | Timestamp | 创建时间。按照 ISO8601 标准表示，并且使用 UTC 时间。格式为：YYYY-MM-DDThh:mm:ssZ。 |
| modifiedTime         | Timestamp | 最后修改时间。按照 ISO8601 标准表示，并且使用 UTC 时间。格式为：YYYY-MM-DDThh:mm:ssZ。 |
| maxRequestNum   | Int    | 请求配额总量，-1表示没有限制。|


## 示例 
```
https://apigateway.api.qcloud.com/v2/index.php?
&<公共请求参数>
&Action=DescribeUsagePlansStatus
&usagePlanIds.0=usagePlan-XX
&usagePlanIds.1=usagePlan-XXXX
&offset=0
&limit=2
&orderby=createdTime
&order=desc
&searchKey=aa
&filter.notServiceId=service-6qdpttk
&filter.environment=Test
```
返回示例如下：
```
{
    "code":"0",
    "message":"",
    "codeDesc":"Success",      
    "totalCount":2,
	"usagePlanStatusSet":[
		{
			"usagePlanId":"usagePlan-XX",
			"usagePlanName":"test1",
			"usagePlanDescription":"test1",
			"maxRequestNumPreSec":500,
			"createdTime":"2017-08-07T00:00:00Z",
			"modifiedTime":"2017-08-07T00:00:00Z",
			"maxRequestNum": -1
		},
		{
			"usagePlanId":"usagePlan-XXXX",
			"usagePlanName":"test2",
			"usagePlanDescription":"test2",
			"maxRequestNumPreSec":500,
			"createdTime":"2017-08-07T00:10:00Z",
			"modifiedTime":"2017-08-07T00:10:00Z",
			"maxRequestNum": 100
		}
	]
}
```




