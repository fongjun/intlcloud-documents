## 操作场景
在服务完成发布后，您可以通过服务的子域名，或您已经绑定的自定义域名访问 API。

## 操作说明
服务提供两种形式的子域名，分别为：`http://{your-unique-id}.{region}.apigateway.myqcloud.com`和`http://{service-id}-{your-unique-id}.{region}.apigw.tencentcs.com`，下文以第一种为例：

在发布后，根据发布环境的不同，具体 API 的访问路径形如`http://{your-unique-id}.{region}.apigateway.myqcloud.com/release`。

例如，您的用户 ID 为2768864771，在广州区（gz）创建了名为`register`的服务，其服务 ID 为 service-n904iiau ，并在其中添加了路径为`/user`的 API，现在发布了`register`服务在发布环境。在这种情况下，如果有其他用户或 App 需要访问`/user`的 API，正确的访问路径为`http://service-n904iiau-2768864771.ap-guangzhou.apigateway.myqcloud.com/release/user`。
