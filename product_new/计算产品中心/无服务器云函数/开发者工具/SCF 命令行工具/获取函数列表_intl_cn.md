SCF CLI 可以通过 `scf function list` 命令查看已部署至云端的函数列表。

## 参数说明
`scf function list` 命令支持的参数如下：

| 参数      | 简写 | 必填 | 描述                                                         | 示例                 |
| --------- | ---- | ---- | ---------------------------------------- | -------------------- |
| namespace | -ns  | 否   | 指定函数所在命名空间                                         | -ns  test-ns         |
| region    | -r   | 否   | 指定函数所在区域，可参见 <a href="https://intl.cloud.tencent.com/document/product/583/17238#Region-List">地域列表</a> | -r ap-shanghai |

>
> - 当不指定 region 和 namespace 时，默认使用 configure 里的 region 和 default 命名空间 。
> - 可执行 `scf configure get` 查看 configure 配置信息。


## 使用示例

- 执行以下命令，获取 configure 默认区域，default 命名空间下的函数列表。
```
$ scf function list
[>] Region:ap-guangzhou
[>] Namespace:default 
Runtime              Status          AddTime              ModTime              FunctionName 
Python2.7            Active          2019-07-10 19:19:41  2019-08-01 17:15:48  proxy_test<br>
```
- 执行以下命令，获取北京区域，所有命名空间下的函数列表。
```bash
$ scf function list -r ap-beijing
[>] Region:ap-beijing
[>] Namespace:toywang 
Runtime              Status          AddTime              ModTime              FunctionName 
Python2.7            Active          2019-07-10 19:19:41  2019-08-01 17:15:48  proxy_test  <br> 
[>] Region:ap-beijing
[>] Namespace:Mason_test 
Runtime              Status          AddTime              ModTime              FunctionName  
Python2.7            Active          2019-06-08 19:37:21  2019-07-30 21:39:42  Thumbnail 
```

- 执行以下命令，获取北京区域，default 命名空间下的函数列表。
```bash
$ scf function list -r ap-beijing -ns default
[>] Region:ap-beijing
[>] Namespace:default 
Runtime              Status          AddTime              ModTime              FunctionName           
Nodejs8.9-service    Active          2019-08-06 11:28:05  2019-08-06 11:28:05  max_log_test 
Nodejs8.9-service    Active          2019-08-05 20:20:18  2019-08-05 20:40:02  nuxt-dev    
Nodejs8.9-service    Active          2019-08-04 19:35:50  2019-08-04 19:35:50  servicetest0805 
```
