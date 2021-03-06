欢迎使用腾讯云监控服务。云监控（Cloud Monitor）为您提供立体化云产品数据监控，智能化数据分析，实时化故障告警和个性化数据报表配置。让您实时、精准掌控业务和各个云产品健康状况。
用户可以使用本文档介绍的 API 对云监控服务进行相关操作，如读取监控数据等，具体支持的操作可参见<a href="https://intl.cloud.tencent.com/document/product/248/4474" title="API概览">API概览</a>。
请确保在使用这些接口前，已充分了解<a href="https://intl.cloud.tencent.com/doc/product/248/967">CM产品</a>及其<a href="https://intl.cloud.tencent.com/doc/product/248/1045">使用方式</a>。


云监控的一些常用术语如下：
## 1.术语表
| 术语 | 全称  | 中文 | 说明 |
|---------|---------|---------|---------|
| Namespace  | Namespace | <a href="https://intl.cloud.tencent.com/doc/product/248/968">命名空间</a> | 命名空间是指标的容器。不同命名空间中的指标彼此独立，因此来自不同应用程序的指标不会被错误地聚合到相同的统计信息中。|
| Metric  | Metric | <a href="https://intl.cloud.tencent.com/doc/product/248/968">指标</a> | 指标是作为监控的变量，数据点代表该变量随时间变化的值。例如，云服务器的CPU利用率是一个指标，云数据库的空间占用率则是另一个指标。|
| Dimension | Dimension |<a href="https://intl.cloud.tencent.com/doc/product/248/968">维度</a> | 维度是对监控对象进行标识的名称/值对结构，用于描述监控对象的特征。|


## 2.API快速入门

通过API使用云监控，您可以直接查询您关注的数据：
您可以使用<a href="/doc/api/405/读取监控数据(新)" title="读取监控数据(新)">读取监控数据(新)</a>来查询数据。



## 3.使用限制

无。




