## 简介
弹性 MapReduce（EMR）结合云技术和 Hadoop、Hive、Spark、Hbase、Presto、Storm 等社区开源技术，提供安全、低成本、高可靠、可弹性伸缩的云端托管 Hadoop 服务。您可以在数分钟内创建安全可靠的专属 Hadoop 集群，以分析位于集群内数据节点或对象存储 COS 上的 PB 级海量数据。

## 功能特性
- 弹性 MapReduce 的软件完全源于开源社区中的 Hadoop 软件，您可以将现有的大数据集群无缝平滑迁移至腾讯云上。弹性 MapReduce 产品中集成了社区中常见的热门组件，包括但不限于 Hive、Hbase、Spark、Presto、Sqoop、Hue 等，可以满足您对大数据的离线处理、流式计算等全方位需求。

- 弹性 MapReduce 无缝集成了腾讯云对象存储（COS）服务，您可将原本存储于 HDFS 中的文件放置在可无限扩展、存储成本低且高可靠的 COS 中，实现计算存储分离。依托于 COS，您可以在需要的时候创建集群，并在任务完成后销毁集群。与此同时，您无需担心数据的丢失。按需创建的集群，可以大幅度降低您的大数据处理成本。

- 弹性 MapReduce 采用了5种节点类型：Master 节点、Core 节点、Task 节点、Router 节点和 Common 节点。各类型节点作用，请参见 [节点类型说明](https://intl.cloud.tencent.com/document/product/1026/31094)。

- 弹性 MapReduce 目前支持了众多资源规格，您可以采用 EMR 标准型、内存型、高 IO、计算型及大数据机型实例作为计算资源。若您需要在黑石物理主机上部署 Hadoop 集群，请 [提交工单](https://console.cloud.tencent.com/workorder/category ) 联系我们。
