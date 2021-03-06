このドキュメントでは、Cloud Virtual Machine（CVM）インスタンスを購入した後、インスタンスにログインできない問題についての解決策を紹介します。CVMに登録できない問題を特定と解決することに役立ちます。

## 故障の主な原因

下記画像にCVMインスタンスが接続できない主な要因分類と出現確率を示します。インスタンスが接続できない場合、インテリジェント診断ツールを用いて、以下の原因により、特定してください。
<img src="https://main.qcloudimg.com/raw/d8e0151489003a251514eebe74dc201a.png" height="310" width="520" />

## 故障処理の考え方

### インスタンスタイプを確認する

まず、購入したインスタンスのタイプがwindowsインスタンスなのかLinuxインスタンスなのかを把握する必要があります。次に、異なるインスタンスのタイプによってCVMにログインできない原因が異なる可能性があります。購入したインスタンスのタイプによって、以下のドキュメントを参照して問題を特定と解決します。
1. [Windows インスタンスにログインできない](http://intl.cloud.tencent.com/document/product/213/10339)
- [Linuxインスタンスにログインできない](https://intl.cloud.tencent.com/document/product/213/32500)

### 検査ツールを通じて原因を診断する
Tencent Cloudが提供された[セルフ診断](https://console.cloud.tencent.com/workorder/check)と[セキュリティグループ(ポート)検証ツール](https://console.cloud.tencent.com/vpc/helper)により、ログインできない原因を判断することができます。このツールを使うと70％ぐらいのログイン問題を検査と特定することができます。

#### セルフ診断ツール
診断した問題には、帯域幅利用率が高すぎ、外部ネットワーク帯域幅が0、CVMの高負荷、セキュリティグループルールの不正、DDoS攻撃、セキュリティ隔離やアカウント支払い延滞などが含まれます。

#### セキュリティグループ（ポート）検証ツール
セキュリティグループとポートに関連する故障を検出します。セキュリティグループ設定に問題があれば、このツールの**ワンクリックポートオーペン**機能を通じて、セキュリティグループのすべての常用インターフェースを開放できます。
このツールを通じて問題の原因を特定した場合、問題原因ガイドによって、該当の故障処理を行ってください。

### インスタンスのリスタート
検査ツールで該当の故障を判断して処理した後、あるいは検査ツールを利用して、ログインできない原因を特定できない場合、インスタンスをリスタートし、再びリモート接続を試して、接続が成功するかどうかを確認します。
インスタンスのリスタートに関する詳細情報は [インスタンスのリスタート](http://intl.cloud.tencent.com/document/product/213/4928)をご参照ください。

### ログイン問題について、他のよくある原因
上記の処理で問題原因を特定できない場合、或いはCVMにログインした際に以下のようなエラー情報が返された場合は、以下の解決案をご参照しください。

#### Windows インスタンス
- [Windows インスタンス：リモートデスクサービスにログインする権限がない](https://intl.cloud.tencent.com/document/product/213/32420)
- [Windows インスタンス：Mac リモートログイン異常](https://intl.cloud.tencent.com/document/product/213/32422)
- [Windows インスタンス：身分認証エラーが発生した](https://intl.cloud.tencent.com/document/product/213/32421)
- [Windows インスタンス：リモートデスクがリモートPCに接続できない](https://intl.cloud.tencent.com/document/product/213/32404)

#### Linuxインスタンス
[Linux インスタンス：CPU とメモリの占用率が高いため、ログインできない](https://intl.cloud.tencent.com/document/product/213/32387)

## 後続操作

上記の処理を実行してもリモートログインできない場合、関連するログと検査結果を保存して、[チケットを発行](https://console.cloud.tencent.com/workorder/category)してフィードバックして問題を解決します。
