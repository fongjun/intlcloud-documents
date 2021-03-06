## 策略创建
若您需要针对项目级别的授权操作进行细化，如数据查询、刷新预热、域名管理操作分别授权给不同的子账号，可通过以下步骤创建策略：
1. 登录 [访问管理控制台](https://console.cloud.tencent.com/cam/overview) ，单击左侧目录的【策略】。
2. 单击【新建自定义策略】，而后选择【按产品功能或项目权限创建】

3. 按要求填充策略名称，在下方服务类型中勾选【内容分发网络】
4. 按需开启需要授权的操作集并关联项目（默认项目不可进行授权），而后关联子用户即可

## 资源级&项目级
目前操作集归类及对应的 OPEN API2.0 及 OPEN API3.0 接口如下所示，拥有操作集权限的子用户，可针对有权限项目内任意一个域名调用列表中的2.0接口及3.0接口：

| 权限集合              | API2.0                                                       | API3.0                                                       | 是否需要授权 |
| :-------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------- |
| 查询消耗数据及统计量  | DescribeCdnHostInfo DescribeCdnHostDetailedInfo GetCdnStatusCode<br/>GetCdnStatTop<br/>GetCdnProvIspDetailStat | DescribeCdnData DescribeOriginData<br/>ListTopData<br/>DescribeIpVisit | 是           |
| 查询域名信息          | GetHostInfoById<br/>GetHostInfoByHost                        | DescribeDomains<br/>DescribeDomainsConfig                    | 是           |
| 查询 CDN 日志下载链接 | GenerateLogList<br/>GetCdnLogList                            | DescribeCdnDomainLogs                                        | 是           |
| 添加域名              | AddCdnHost                                                   | AddCdnDomain                                                 | 是           |
| 上线 / 下线域名       | OnlineHost OfflineHost                                       | StartCdnDomain<br/>StopCdnDomain                             | 是           |
| 删除域名              | DeleteCdnHost                                                | DeleteCdnDomain                                              | 是           |
| 修改域名配置          | UpdateCdnConfig                                              | UpdateDomainConfig                                           | 是           |
| 刷新预热              | RefreshCdnDir<br/>RefreshCdnUrl<br/>GetCdnRefreshLog<br/>CdnPusherV2<br/>GetPushLogs<br/>CdnOverseaPushser | PurgeUrlsCache<br/>PurgePathCache<br/>DescribePurgeTasks<br/>PushUrlsCache<br/>DescribePushTasks | 是           |
| 服务查询              | QueryCdnIp（无需授权）                                       | DescribeCdnIp                                                | 是           |

## 控制台权限
- 查看消耗数据及统计量：若策略开启了【查看消耗数据及统计量】并关联项目，则可查看控制台以下模块信息：
  - 概览页：仅数据展示模块
  - 统计分析：实时监控
  - 统计分析：数据分析
  - 全网数据监控
- 查询域名信息：若策略开启了【查询域名信息】并关联项目，则在控制台【域名管理】页面查看有权限的项目中域名列表及详细配置信息。
- 查询 CDN 日志下载链接：若策略开启了【查询 CDN 日志下载链接】并关联项目，则在控制台【日志服务】页面，可查询访问日志下载链接。
- 添加域名：若策略开启了【添加域名】并关联项目，则可向指定项目中添加域名。
- 上线 / 下线域名：若策略开启了【上线 / 下线域名】并关联项目，则可上线 / 下线指定项目中的加速域名。
- 删除域名：若策略开启了【删除域名】并关联项目，则可删除指定项目中的加速域名，删除域名需要为下线状态。因此若需要删除一个上线状态的域名，需要具备【上线 / 下线域名】权限。
- 修改域名配置：若策略开启了【修改域名配置】并关联项目，则可以修改指定项目中的加速域名配置。
- 刷新预热：若策略开启了【刷新预热】并关联项目，则可以在【刷新缓存】页面提交对应的刷新、预热（白名单）任务，并查询刷新预热任务的执行状态。
