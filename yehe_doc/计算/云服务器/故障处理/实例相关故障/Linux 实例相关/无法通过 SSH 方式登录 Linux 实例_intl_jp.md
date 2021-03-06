本ドキュメントでは、CentOS 7.5 OSのCloud Virtual Machineを例として、 SSH を利用してLinux インスタンスにログインできない問題をトラブルシューティングする方法について説明します。

## 故障について

[SSHを介してLinux インスタンスにログインする](https://cloud.tencent.com/document/product/213/35700)時、「接続ができない」または「接続に失敗しました」というエラーメッセージが表示されます。

## 問題の特定とトラブルシューティング
### ステップ1：セキュリティグループルールの設定を確認する

[セキュリティグループ（ポート）検証ツール](https://console.cloud.tencent.com/vpc/helper) を使用してセキュリティグループルールの設定が正しいかどうかを確認します。
- セキュリティグループのポート設定に問題があると確定した場合、ツールの【Open all ports】機能を利用してポートを開放します。また、実際のニーズに応じてセキュリティグループルールをカスタマイズ設定することもできます。
- セキュリティグループのポート設定に問題がない場合、 [次へ](#step07)を実行してください。
 
###  ステップ2： sshd サービスポートを確認する
1. <span id="step07">[ VNC を介して Linux インスタンスにログインします](https://cloud.tencent.com/document/product/213/35701)。</span>
2. OSのインターフェースで、下記のコマンドを実行し、ポートがSSHDサービスによってリッスンされているかどうかを確認します。
```
netstat -tnlp | grep sshd
```
 - 下記の結果が返された場合、 sshd プロセスが既に22ポートをリッスンしていることを示します、[チケットを送信](https://console.cloud.tencent.com/workorder/category)してフィードバックしてください。
```
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1015/sshd  
```
 - 結果が返されない場合は、 sshd サービスが起動していない可能性があります。[ステップ3](#step09)を実行してください。
 
###  ステップ3： sshd サービスが起動しているかどうかを確認する
<span id="step09">下記のコマンドを実行し、 sshd サービスが起動しているかどうかを確認します。</span>
```
systemctl status sshd.service
```
 - sshd サービスが起動している場合は、[チケットを送信](https://console.cloud.tencent.com/workorder/category) してフィードバックしてください。
 - sshdサービスが起動しない場合、下記のコマンドを実行し、 sshd サービスを起動して、SSHを介してLinuxインスタンスに再度ログインします。
```
systemctl start sshd
```

上記操作を実行しても、インスタンスに接続できない場合は、 [チケットを送信](https://console.cloud.tencent.com/workorder/category) してフィードバックするのをおすすめします。

