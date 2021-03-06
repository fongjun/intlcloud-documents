インスタンスの安全性と信頼性を確保するために、Tencent Cloudは[パスワードログイン](/doc/product/213/6093) とSSH キーペア・ログインの2種類の暗号化ログイン方式を提供します。本ドキュメントはSSH キーペア・ログインの設定について説明します。
[Linux CVM のカスタマイズ設定](/doc/product/213/10517#.E8.AE.BE.E7.BD.AE.E4.BF.A1.E6.81.AF) を行う時、SSHキーはCVMの暗号化ログイン方式として選択することができます。

## SSH キーの概要
Tencent Cloudではパブリックキー暗号化方式でLinuxインスタンスへのログイン情報を暗号化と復号化するのは可能です。パブリックキー暗号化方式はパブリックキーを使用して、あるデータ（例えばパスワード）を暗号化して、受信者がプライベートキーを使用してデータを復号化します。パブリックキーとプライベートキーの組み合わせはキーペアと呼ばれます。キーペアを利用してCVMに安全に接続できます、通常のパスワードよりも安全なCVMログイン方式です。

Tencent Cloudはパブリックキーのみを保存するため、ユーザーがプライベートキーは保管する必要があります。プライベートキーを持っていれば、誰でもログイン情報を復号化できますので、プライベートキーを安全な場所に保管するのは重要です。


## 機能と優位性
従来のユーザー名とパスワードの認証方式より、SSHキーは以下のメリットがある：
- SSH キーを利用してログイン認証はより高い安全性と信頼性を提供して、総当たり攻撃の脅威を防ぎます。
- SSH キーログイン方式はもっと簡単で、コンソールとローカルクライアントで簡単に設定するだけで、リモートでインスタンスにログインできます。再度ログインする時にパスワードを入力する必要がありません。

## 使用制限
- Linux インスタンスのみをサポートします。
Tencent Cloudはプライベートキー情報を保存しないので、SSHキーを作成してから10分以内に「ダウンロード」をクリックしてプライベートキーを取得し、大切に保管してください。
- 一つのLinuxインスタンスは一つのSSHキーのみをバインドできます。インスタンスはSSHキーをバインドされた場合、新しいキーをバインドすると、元のキーが置き換えられます。
- データの安全性を確保するために、CVMがシャットダウンの状態でキーをロードしてください。


## ユースケース


[SSH キー操作ガイド](https://intl.cloud.tencent.com/document/product/213/16691)をご参照ください。
