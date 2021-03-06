腾讯云云监控事件中心，产品事件当前支持以下事件类监控信息：
<html>

<style type="text/css">
.div-table-1 {width:100%;}
.div-table-1 td{white-space:pre-wrap; }
.div-td-el{word-wrap:break-word;word-break:break-all;}
.div-td-1{width:12%}
.div-td-2{width:12%}
.div-td-3{width:8%}
.div-td-4{width:12%}
.div-td-5{width:8%}
.div-td-6{width:20%}
.div-td-7{width:28%}
</style>
<h2>云服务器</h2>
<table class="div-table-1">
<thead>
<tr>
<th class="div-td-1">事件<br>中文名</th>
<th class="div-td-2">事件<br>英文名</th>
<th class="div-td-3">事件<br>类型</th>
<th class="div-td-4">从属维度</th>
<th class="div-td-5">有无<br>恢复<br>概念</th>
<th class="div-td-6">事件描述</th>
<th class="div-td-7">处理方法与建议</th>
</tr>
</thead>
<tbody><tr>
<td class="div-td-1">内核故障</td>
<td class="div-td-2 div-td-el">GuestCoreError</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">无</td>
<td class="div-td-6">操作系统内核 bug 或驱动问题导致操作系统内核发生致命错误</td>
<td class="div-td-7">1.检查系统中是否有加载内核自身携带的内核驱动以外的其它内核驱动模块。尝试不要加载这些模块，观察系统运行情况。<br> 2.关注内核与操作系统发行版的 bug 报告，尝试升级内核解决。<br>3.腾讯云云服务器默认会开启 kdump，发生 panic 时一般会在/var/crash下生成系统内存 dump 信息，可以利用 crash 工具进行分析</td>
</tr>
<tr>
<td class="div-td-1">内存 OOM</td>
<td class="div-td-2 div-td-el" >GuestOom</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">无</td>
<td class="div-td-6">系统内存使用过载</td>
<td class="div-td-7">1.评估当前系统所配置内存是否满足业务内存需求，如果需要更大的内存建议升级 CVM 内存配置。<br>2.通过系统日志（dmesg，/var/log/messages等）查看 OOM 发生时 kill 掉的进程，查看进程内存使用是否符合预期，通过 valgrind 等工具分析是否存在内存泄露</td>
</tr>
<tr>
<td class="div-td-1">ping 不可达</td>
<td class="div-td-2 div-td-el" >PingUnreachable</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-6">云服务器网络ping不可达</td>
<td class="div-td-7">1.检查云服务器当前的运行状态是否正常，如果发生了系统挂死等情况，可以从控制台重启（强制重启）恢复。<br>2.如果云服务器当前运行正常，检查云服务器网络配置，包括：云服务器内部的网络服务、防火墙、云服务器安全组设置等</td>
</tr>
<tr>
<td class="div-td-1">磁盘只读</td>
<td class="div-td-2 div-td-el">DiskReadonly</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-6">磁盘无法写入数据</td>
<td class="div-td-7">1.检查磁盘的存储是否存在已满情况。<br>2.Linux 操作系统可以查看的 inode（df -i）是否有耗尽的情况。<br>3.检查文件系统是否损坏</td>
</tr>
<tr>
<td class="div-td-1">机器重启</td>
<td class="div-td-2 div-td-el">GuestReboot</td>
<td class="div-td-3">状态<br>变更</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-6">云服务器重启</td>
<td class="div-td-7">当云服务器被重启的时候，会触发该事件，请根据实际情况查看状态变更是否符合预期</td>
</tr>
<tr>
<td class="div-td-1">外网出带宽超限导致丢包</td>
<td class="div-td-2 div-td-el">PacketDroppedByQosWanOutBandwidth</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-6">云服务器实例外网出带宽超过主机的外网出带宽限制导致丢包。带宽毛刺导致的丢包不会体现在带宽视图中，原因：带宽最细统计粒度为10级（10秒内总流量/10秒）。若常量带宽没有明显超出也可忽略。</td>
<td class="div-td-7">提高外网带宽上限。<br>如果已达到可购买的最高上限，<br>可通过负载均衡等方式降低单机带宽</td>
</tr>
<tr>
<td class="div-td-1">连接数超限导致丢包</td>
<td class="div-td-2 div-td-el">PacketDroppedByQosConnectionSession</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">云服务器实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-6">云服务器实例连接数过多导致丢包</td>
<td class="div-td-7">联系售后服务人员</td>
</tr>
</tbody></table>

<h2>负载均衡</h2>
<table class="div-table-1">
<thead>
   <tr>
     <th class="div-td-1">事件<br>中文名</th>
     <th class="div-td-2">事件<br>英文名</th>
     <th class="div-td-3">事件<br>类型</th>
     <th class="div-td-4">从属维度</th>
     <th class="div-td-5">有无<br>恢复概念</th>
     <th class="div-td-6">事件描述</th>
     <th class="div-td-7">处理方法与建议</th>
   </tr>
 </thead>
 <tbody>
   <tr>
      <td class="div-td-1">外网IP被封堵</td>
      <td class="div-td-2 div-td-el">VipBlockInfo</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">负载均衡实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">负载均衡公网IP被攻击，安全侧检测异常后封堵</td>
      <td class="div-td-7">提工单查询具体的封堵原因和解封策略</td>
   </tr>
    <tr>
      <td class="div-td-1">服务器端口状态异常</td>
      <td class="div-td-2 div-td-el">RsPortStatusChange</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">后端服务器端口维度</td>
      <td class="div-td-5">有 </td>
      <td class="div-td-6">外网LB后端服务端口健康检查异常</td>
      <td class="div-td-7">查看后端服务端口的服务状态</td>
   </tr>
 </tbody>
   </table>

<h2> VPN网关</h2>
<table class="div-table-1">
<thead>
<tr>
<th class="div-td-1">事件<br>中文名</th>
<th class="div-td-2">事件<br>英文名</th>
<th class="div-td-3">事件<br>类型</th>
<th class="div-td-4">从属维度</th>
<th class="div-td-5">有无<br>恢复概念</th>
<th class="div-td-7">事件描述</th>
<th class="div-td-6">处理方法与建议</th>
</tr>
</thead>
<tbody>
<tr>
<td class="div-td-1">外网出带宽超限导致丢包</td>
<td class="div-td-2 div-td-el">PacketDroppedByQosWanOutBandwidth</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">VPN网关实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-7">VPN 的外网出带宽超过限制导致丢包。带宽毛刺导致的丢包不会体现在带宽视图中，原因：带宽最细统计粒度为10级（10秒内总流量/10秒）。若常量带宽没有明显超出也可忽略。</td>
<td class="div-td-6">提高外网带宽上限</td>
</tr>
<tr>
<td class="div-td-1">连接数超限导致丢包</td>
<td class="div-td-2 div-td-el">PacketDroppedByQosConnectionSession</td>
<td class="div-td-3">异常<br>事件</td>
<td class="div-td-4">VPN网关实例维度</td>
<td class="div-td-5">有</td>
<td class="div-td-7">VPN网关实例连接数过多导致丢包</td>
<td class="div-td-6">联系售后服务人员</td>
</tr>
</tbody>
</table>

<h2>容器服务</h2>
<table class="div-table-1">
<thead>
<tr>
	 <th class="div-td-1">事件<br>中文名</th>
     <th class="div-td-2">事件<br>英文名</th>
     <th class="div-td-3">事件<br>类型</th>
     <th class="div-td-4">从属维度</th>
     <th class="div-td-5">有无<br>恢复概念</th>
     <th class="div-td-6">事件描述</th>
     <th class="div-td-7">处理方法与建议</th>
</tr>
</thead>
<tr>
     <td class="div-td-1">节点异常</td>
     <td class="div-td-2 div-td-el">NodeNotReady</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">有</td>
     <td class="div-td-7">节点异常可能由多种原因导致，例如网络不通，节点 kubelet 异常，容器造成 OOM 等都会致使此异常发生。节点长时间异常，会造成 kubenetes 主动驱逐该节点上的容器。</td>
     <td class="div-td-6">1.首先排查节点在 CVM 页面是否运行中，监控是否有异常。<br>2.登录到子机查看kubelet是否运行正常。3.登录到子机查看 docker 是否运行正常</td>
</tr>
<tr>
     <td class="div-td-1">节点磁盘空间将要耗尽</td>
     <td class="div-td-2 div-td-el">NodeHasDiskPressure</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">有</td>
     <td class="div-td-7">节点上容器和镜像存储所用到的磁盘（cbs 或者 root）空间将要耗尽，如果耗尽，将触发 NodeOutOfDisk，会致使新的容器无法在被调度到此节点</td>
     <td class="div-td-6">清理磁盘或者无用的容器镜像</td>
</tr>
<tr>
     <td class="div-td-1">节点磁盘空间已耗尽</td>
     <td class="div-td-2 div-td-el">NodeOutOfDisk</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">有</td>
     <td class="div-td-7">节点上容器和镜像存储所用到的磁盘（cbs 或者root）空间已经耗尽，新的容器无法在被调度到此节点</td>
     <td class="div-td-6">清理磁盘或者无用的容器镜像</td>
</tr>
<tr>
     <td class="div-td-1">清理磁盘或者无用的容器镜像</td>
     <td class="div-td-2 div-td-el">NodeHasInsufficientMemory</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">有</td>
     <td class="div-td-7">节点内存使用率偏高</td>
     <td class="div-td-6">扩容或者调度容器到其他节点</td>
</tr>
<tr>
     <td class="div-td-1">节点 OOM</td>
     <td class="div-td-2 div-td-el">SystemOOM</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">无</td>
     <td class="div-td-7">节点由于内存过高而发生 OOM</td>
     <td class="div-td-6">检查当前节点上触发 OOM 的原因，例如查看监控，syslog，demsg 等</td>
</tr>
<tr>
     <td class="div-td-1">节点节点网络不可达</td>
     <td class="div-td-2 div-td-el">NodeNetworkUnavailable</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">无</td>
     <td class="div-td-7">节点上网络没有被正确配置， 正常情况下，从控制台或云API创建的集群不会出现此问题</td>
     <td class="div-td-6">及时提交工单或与售后服务人员联系</td>
</tr>
<tr>
     <td class="div-td-1">节点 Inode 不足</td>
     <td class="div-td-2 div-td-el">NodeInodePressure</td>
     <td class="div-td-3">异常<br>事件</td>
     <td class="div-td-4">集群维度 </td>
     <td class="div-td-5">无</td>
     <td class="div-td-7"> 节点 Inode 不足会造成节点无法创建新的容器</td>
     <td class="div-td-6">检查节点上Inode的剩余情况，可以尝试清理无用的容器镜像释放 Inode 空间</td>
</tr>
</tbody>
</table>

<h2>云数据库 MySQL</h2>
<table class="div-table-1">
<thead>
<tr>
      <th class="div-td-1">事件<br>中文名称</th>
      <th class="div-td-2">事件<br>英文名称</th>
      <th class="div-td-3">事件<br>类型</th>
      <th class="div-td-4">从属维度</th>
      <th class="div-td-5">有无<br>恢复概念</th>
      <th class="div-td-6">事件描述</th>
      <th class="div-td-7">处理方法和建议</th>
</tr>
</thead>
<tbody>
   <tr>
      <td class="div-td-1">内存 OOM</td>
      <td class="div-td-2 div-td-el">OutOfMemory</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MySQL 实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">数据库内存使用过载</td>
      <td class="div-td-7">评估当前数据库内存规格是否满足业务需求，如果需要更大的内存建议升级 MySQL 的内存配置</td>
   </tr>
   <tr>
      <td class="div-td-1">主从切换</td>
      <td class="div-td-2 div-td-el">PrimarySwitch</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MySQL 实例维度</td>
      <td class="div-td-5">无</td>
      <td class="div-td-6">主实例和从实例发生切换</td>
      <td class="div-td-7">当物理机故障时可能会触发该事件，请确认实例状态是否正常</td>
   </tr>
   <tr>
      <td class="div-td-1">只读实例剔除</td>
      <td class="div-td-2 div-td-el">RORemoval</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MySQL 实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">只读实例故障或超过延迟阈值</td>
      <td class="div-td-7">若只读组仅一个只读实例，只读实例被剔除后，请及时对读流量进行切换，避免因只读实例出现单点故障，建议为只读组至少购买两个只读实例</td>
   </tr>
   <tr>
      <td class="div-td-1">服务器故障导致实例迁移</td>
      <td class="div-td-2 div-td-el">ServerfailureInstanceMigration</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MySQL 实例维度</td>
      <td class="div-td-5">无</td>
      <td class="div-td-6">服务器故障发起的实例迁移</td>
      <td class="div-td-7">迁移切换时间以维护时间窗为准，若需要更改切换时间，请及时调整，切换时间将以调整后的维护时间窗为准
      </td>
   </tr>
</tbody>
</table>

<h2>云数据库 MongoDB</h2>
<table class="div-table-1">
<thead>
	<tr>
      <th class="div-td-1">事件<br>中文名称</th>
      <th class="div-td-2">事件<br>英文名称</th>
      <th class="div-td-3">事件<br>类型</th>
      <th class="div-td-4">从属维度</th>
      <th class="div-td-5">有无<br>恢复概念</th>
      <th class="div-td-6">事件描述</th>
      <th class="div-td-7">处理方法和建议</th>
	</tr>
	<tr>
      <td class="div-td-1">备份 oplog 不足</td>
      <td class="div-td-2 div-td-el">oplogInsufficient</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MongoDB 实例维度</td>
      <td class="div-td-5">无</td>
      <td class="div-td-6">云数据库 MongoDB 在备份时，无法读取到上次备份到本次备份的完整 oplog，这将影响您的数据库回档到 7 天内的任意时间点 </td>
      <td class="div-td-7">建议在腾讯云官网控制台调整云数据库 MongoDB oplog 的大小或备份频率；如您不需要该事件通知，可以在腾讯云官网 MongoDB 控制台备份界面进行设置以关闭该事件通知</td>
    </tr>
    <tr>
      <td class="div-td-1">连接数超限</td>
      <td class="div-td-2 div-td-el">connectionOverlimit</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MongoDB 实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">实例连接数使用超过限制</td>
      <td class="div-td-7">评估当前实例所配置连接数是否满足业务需求，如果需要更大的连接配置建议升级腾讯云 MongoDB 数据库实例配置</td>
    </tr>
    <tr>
      <td class="div-td-1">主从切换</td>
      <td class="div-td-2 div-td-el">primarywitch</td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MongoDB 实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">实例 Primary 和 Secondary 切换</td>
      <td class="div-td-7">当物理机故障时可能会触发该事件，请确认实例状态是否正常</td>
    </tr>
    <tr>
      <td class="div-td-1">磁盘空间已耗尽</td>
      <td class="div-td-2 div-td-el">instanceOutOfDisk </td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MongoDB 实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">磁盘空间写满，造成实例只读</td>
      <td class="div-td-7">清理磁盘空间</td>
    </tr>
    <tr>
      <td class="div-td-1">实例 Rollback </td>
      <td class="div-td-2 div-td-el">instanceRollback </td>
      <td class="div-td-3">异常<br>事件</td>
      <td class="div-td-4">云数据库 MongoDB 实例维度</td>
      <td class="div-td-5">有</td>
      <td class="div-td-6">实例数据 rollback</td>
      <td class="div-td-7">当主节点有部分数据还没有及时同步到从节点时主节点故障并发生主从切换可能会触发该事件，请确认实例状态是否正常</td>
    </tr>
</thead>
</table>

<h2>专线接入（物理专线，专用通道）</h2>
<table class="div-table-1">
<thead>
		<tr>
         <th class="div-td-1">事件<br>中文名</th>
         <th class="div-td-2">事件<br>英文名</th>
         <th class="div-td-3">事件<br>类型</th>
         <th class="div-td-4">从属维度</th>
         <th class="div-td-5">有无<br>恢复概念</th>
         <th class="div-td-6">事件描述</th>
         <th class="div-td-7">处理方法和建议</th>
		</tr>
    </thead>
<tbody>
		<tr>
			<td class="div-td-1">物理专线Down</td>
			<td class="div-td-2 div-td-el">DirectConnecDown</td>
			<td class="div-td-3">异常<br>事件</td>
			<td class="div-td-4">物理专线维度</td>
			<td class="div-td-5">有</td>
			<td class="div-td-6">专线物理链路传输中断或异常</td>
			<td class="div-td-7">1. 检查物理线路是否有异常中断情况（如光纤被挖断，线路被拔出设备等）<br> 2. 检查对接端口及光/电模块是否正常<br>3. 检查网络设备端口是否被关闭</td>
		</tr>
		<tr>
			<td class="div-td-1">专用通道Down</td>
			<td class="div-td-2 div-td-el">DirectConnectTunnelDown</td>
			<td class="div-td-3">异常<br>事件</td>
			<td class="div-td-4">专用通道维度</td>
			<td class="div-td-5">有</td>
			<td class="div-td-6">专线物理链路传输中断或异常</td>
			<td class="div-td-7">1. 检查物理线路是否有异常中断情况（如光纤被挖断，线路被拔出设备等）<br>2. 检查对接端口及光/电模块是否正常<br>3. 检查网络设备端口是否被关闭</td>
		</tr>
		<tr>
			<td class="div-td-1">专用通道BGP会话Down</td>
			<td class="div-td-2 div-td-el">DirectConnectTunnelBGPSessionDown</td>
			<td class="div-td-3">异常<br>事件</td>
			<td class="div-td-4">专用通道维度</td>
			<td class="div-td-5">有</td>
			<td class="div-td-6">专用通道BGP会话状态中断</td>
			<td class="div-td-7">1. 检查网络设备BGP进程是否正常<br>2. 检查专用通道是否正常<br>3. 检查物理线路是否正常</td>
		</tr>	
		<tr>
			<td class="div-td-1">BGP通道路由条目超限告警</td>
			<td class="div-td-2 div-td-el">DirectConnectTunnelRouteTableOverload</td>
			<td class="div-td-3">异常<br>事件</td>
			<td class="div-td-4">专用通道维度</td>
			<td class="div-td-5">无</td>
			<td class="div-td-6">专用通道BGP会话通道路由条目超过80%</td>
			<td class="div-td-7">1. 检查专用通道BGP会话发布路由条目是否达到限制条目的80%（默认限制100条，<a href="https://intl.cloud.tencent.com/document/product/216/546">详情请参见</a></td>
		</tr>
</tbody>
</table>

</html>

