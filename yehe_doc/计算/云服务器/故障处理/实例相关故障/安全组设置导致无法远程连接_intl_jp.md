
本ドキュメントでは、CVMはセキュリティグループの設定の原因で、リモート接続できない問題のトラブルシューティング方法と解決案について説明します。

## 検査ツール

Tencent Cloudが提供する[セキュリティグループ（ポート）検証ツール](https://console.cloud.tencent.com/vpc/helper)を使用して、リモート接続できないことがセキュリティグループ設定に関連しているかどうかを判断できます。
1. [セキュリティグループ（ポート）検証ツール](https://console.cloud.tencent.com/vpc/helper)にログインします。
2. **Port Verification**画面で、検出対象のインスタンスを選択し、【Quick Check】をクリックします。 下記画像に示すように：
![](https://main.qcloudimg.com/raw/a792a1692e0a21b3f9dfe111d4b86789.png)
このインスタンスが開放していないポートを検出された場合、【Open all ports】機能を利用して、サーバーで一般的に使用しているポートを開放し、リモートログインを再試行します。
<img src="https://main.qcloudimg.com/raw/a743739b5885874c15a6b5c7869f5acd.png" height="400" width="420">


## セキュリティグループの設定を修正する

ツールチェックを利用して、セキュリティグループのポート設定に問題があることが確認されたら、【Open all ports】機能を利用してCVMのすべての共通ポートを開放したくない、またはリモートログインポートをカスタマイズする必要がある場合、セキュリティグループのインバウンドとアウトバウンドルールをカスタマイズして、リモート接続の問題を解決できます。 詳細の操作については、[セキュリティグループルールの修正](https://intl.cloud.tencent.com/document/product/213/18197)をご参照ください。
