## 操作シナリオ 

x86サーバーには、**APM**（Advanced Power Management、高度な電源管理）と **ACPI**（Advanced Configuration and Power Interface、高度なコンフィグレーションと電源インターフェース）といった二つの電源管理方法があります。ACPIはIntel、Microsoftと東芝が共同で開発した電源管理規格で、コンピュータとデバイスを管理するための便利なインターフェースを提供します。一方、APMは電源管理の古い規格です。
LinuxはAPMとACPIを対応していますが、同時に両方を実行することができません。デフォルトでは、LinuxはACPIを実行します。Tencent CloudはACPI電源管理方法を使用することも推奨しています。
LinuxシステムではACPIの管理プログラムをインストールしないと、ソフトシャットダウン処理が失敗してしまいます。当該ドキュメントは、ACPIのインストール状況及びインストール手順の確認について説明します。

## インストール説明

CoreOSシステムの場合、ACPIをインストールする必要がありません。

## 操作手順
 
1. 以下のコマンドを実行し、ACPIのインストールされているかどうかを確認します。
```
ps -ef|grep -w "acpid"|grep -v "grep"
```
 - 該当のプロセスが存在しない場合、ACPIがインストールされていないことを示し、次のステップを実行してください。
 - 該当のプロセスが存在する場合、ACPIがすでにインストール済みのため、タスクが完了です。
2. OS種類により、異なるのコマンドを実行し、ACPIをインストールします。
 - Ubuntu / Debianシステムの場合、次のコマンドを実行します。
```
sudo apt-get install acpid
```
 - Redhat / CentOSシステムの場合、次のコマンドを実行します。
```
yum install acpid
```
 - SUSEシステムの場合、次のコマンドを実行します。
```
in apcid
```
