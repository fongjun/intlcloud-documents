## 计费模式

- 同地域对等连接：免费。
- 跨地域对等连接费用由对等连接发起方支付，计费模式：
  - 日峰值计费，说明请参见下文 **日峰值计费**。

### 日峰值计费

- 计算公式：

  每日费用 = 当日带宽峰值 * 带宽所在阶梯的单价 。

  > **注意：**
  > 当日带宽峰值计算方式：每 5 分钟采集一次，取当日出入带宽的最大值作为每日带宽峰值。

- **计费周期 & 计算周期：**日

- **计费方式：**日后付费

- **价格总览**：

  | 功能           | 计费模式                                                     | 配置              | 价格                     |                                                              |                                                              |
  | -------------- | ------------------------------------------------------------ | ----------------- | ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
  |                |                                                              |                   | 中国大陆（所有地区）互通 | 中国大陆（所有地区）——中国大陆外地域（Hong   Kong、Korea、 Frankfurt、 Silicon Valley 、Virginia   Mumbai、Singapore、Toronto、bangkok） | 中国大陆外地域互通（Hong   Kong、Korea、 Frankfurt、 Silicon Valley 、Virginia   Mumbai、Singapore、Toronto、bangkok） |
  | 同地域对等连接 | 免费                                                         |                   |                          |                                                              |                                                              |
  | 跨地域对等连接 | 当日带宽峰值            按天计费（USD / Mbps / 天）            峰值带宽按每 5 min 平均带宽计 | (0 , 20] Mbps     | 3.19                     | 15                                                           | 15                                                           |
  |                |                                                              | (20 ,100] Mbps    | 1.98                     | 12                                                           | 12                                                           |
  |                |                                                              | (100 , 500] Mbps  | 1.48                     | 9                                                            | 9                                                            |
  |                |                                                              | (500 , 2000] Mbps | 1.19                     | 6                                                            | 6                                                            |
  |                |                                                              | 大于 2000 Mbps    | 0.82                     | 5                                                            | 5                                                            |

> **注意：**
>
> 1. 更多地域价格咨询，请与您的商务经理洽谈。
> 2. 为了方便您查看费用，账单系统中把对等连接描述为：跨地域互通（大陆）账单：对等连接两端都在大陆地域的账单。
> 3. 基础网络跨地域互联暂 **不支持的地域** 是：上海金融、深圳金融、硅谷。

- **日峰值计费示例：**
  如果对等连接发起方在上海，接受方在广州，当日的出带宽峰值为 20Mbps，入带宽峰值为 30 Mbps，那么当日费用为 30* 1.98 =59.4 USD，由发起方支付。