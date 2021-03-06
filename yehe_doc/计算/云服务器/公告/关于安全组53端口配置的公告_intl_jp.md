## 概要
ポート53は DNS（Domain Name Server、ドメイン名サーバー）サーバーによって開放され、主にドメイン名の解析に使用されます。DNS サーバーを通してドメイン名とIPアドレス間の変換を実現し、ドメイン名を記憶すれば、ウェブサイトに迅速にアクセスできます。

『電気通信業務分類ディレクトリ』（2015 版）はインターネットドメイン名の再帰解決サービスが電気通信業務に組み込まれました（コードネームはB26-1）。すなわち、ドメイン名の再帰サービスに従事するには、該当業務の付加価値通信業務ライセンスを取得する必要があります。

## 関連する政策と法律規制要件

**1、ビジネスライセンスを取得していない場合、インターネットドメイン名の解析業務に従事することはできません。**
貴社が当該業務に従事している場合、「エンコードとプロトコル変換業務ライセンス」を取得する必要があり、詳細の申し込み方法は、所属の省の通信管理局にお問い合わせください。。
**『電気通信業務許可の管理措置』第四十六条：**本管理措置の第十六条の第一項規約および第二十八条の第一項規約により、許可されていない電気通信業務または範囲外の電気通信業務に従事している場合、『中華人民共和国の電気通信規則』第六十九条規約に基づく処罰し、状況が深刻な場合、業務中止と命じられ、電気通信業務経営の不正行為リストに加入されます。
**工業と情報部『インターネットドメイン名の管理措置』第三十六条：**ドメイン名の解析サービスを提供する場合、関連法律、規制、基準を順守し、対応する技術、サービス、ネットワークおよび情報セキュリティ保証機能を備え、ネットワークおよび情報セキュリティの保証措置があり、法律に従ってドメイン名の解析ログ、メンテナンスログ及び変更記録を記録・保留し、解析サービスの品質と解析システムのセキュリティを保証します。電気通信業務に従事する場合、電気通信業務のビジネスライセンスを法律に従って取得する必要があります。
**2、Tencent Cloudでは、法律に従ってビジネスライセンスを取得していない、または非経営インターネット情報サービスICP申告手続きを遂行する会社または個人にアクセスや料金請求代理などのサービスを提供することはできません。**
**『電気通信業務許可の管理措置』：第二十四条アクセスサービスを提供する付加価値通信サービスの運営者は、次の規制を遵守する必要がある：**（三）法律に従ってビジネスライセンスを取得していない、または非経営インターネット情報サービスICP申告手続きを遂行する会社または個人にアクセスや料金請求代理などのサービスを提供することはできません。
個人（会社）は**インターネットドメイン名の解析サービス業務**を従事していない場合、サーバーのセキュリティグループポリシーを調整、インバウンドルールのポート53を無効にすることを推奨します。

## インバウンドルールのポート53を無効にする手順

1.  [Tencent Cloud Cloud Virtual Machine コンソール](https://console.cloud.tencent.com/cvm/index)にログインします。
2. インスタンスの管理画面で、ポート53を無効にしたいインスタンスを選択し、当該インスタンスの「ID/名称」をクリックします。下図に示すように：
![](https://main.qcloudimg.com/raw/186bd6ec5c69b12b3ea9645ff1dbb22b.png)
3. インスタンスの詳細画面で、【セキュリティグループ】タブを選択して、インスタンスのセキュリティグループ管理画面に入ります。下図に示すように：
![](https://main.qcloudimg.com/raw/7eb1b0b56520701fc8d28a14cfecd7f1.png)
4.【Bound to security group】で、修正するインバウンドルールの「セキュリティグループ ID/名称」を選択します。
5.「Security Group Rule」画面で、【Inbound rule】タブを選択し、【Add a Rule】をクリックします。下図に示すように：
![](https://main.qcloudimg.com/raw/f1f7f9ce6d3e259e06542bf19d797022.png)
6. ポップアップした「Add Inbound rule」ウインドウで、以下の情報を入力します。下図に示すように：
![インバウンドルールを追加する-53ポートを無効にする](https://main.qcloudimg.com/raw/f3890575ef22f31f67c0c6902f6df55a.png)
 - Type：「Custom」を選択します。
 - Source：「0.0.0.0/0」を入力します。
 - Protocol port：「UDP:53」を入力します。
 - Policy：「Refuse」を選択します。
7.【Completed】をクリックすると、53ポートが無効になります。

## よくある質問

### インターネットドメイン名の解析サービスとは。
**インターネットドメイン名の解析**とはインターネットドメイン名とIPアドレスの対応関係を実現するプロセスです。
**インターネットドメイン名の解析サービス業務**とは、インターネット上でドメイン名の解析サーバーと関連ソフトウェアをセットアップすることにより、インターネットドメイン名とIPアドレス間の対応関係の変換サービスを指します。ドメイン名の解析サービスには、権威解析サービスと再帰的解析サービスが含まれます。
- 権威解析：ルートドメイン名、トップレベルドメイン名およびその他のドメイン名のドメイン名の解析サービスを指します。
- 再帰的解析：ローカルキャッシュまたは権威解析サービスシステムを確認することにより、ドメイン名とIPアドレス間の対応関係を実現するサービスを指します。

インターネットドメイン名の解析サービスは特に再帰解析サービスを指します。詳細については [『電気通信業務分類ディレクトリ（2015年版）』：B26-1  インターネットドメイン名の解析サービス業務](http://www.miit.gov.cn/n1146285/n1146352/n3054355/n3057709/n3057714/c4564270/content.html)をご参照ください。


### インバウンドルールによってポート53を無効にすると、サーバーにどのような影響がありますか。
インターネットドメイン名の解析サービス業務に従事していない場合、インバウンドルールが53ポートを無効にするのはサーバー、業務に影響されません。

### 個人ではインターネットドメイン名の解析サービス業務に従事できますか。
電気通信業務の経営者は法律に基づいて設立された会社である必要があります。個人ではこの業務に従事できません。インターネットドメイン名の解析業務に従事するには、「エンコードとプロトコル変換ビジネスライセンス」を申請する必要があります。ビジネスライセンスの申し込み方法については、所属する省の通信管理局にお問い合わせください。

### 関連するビジネスライセンスなしでインターネットドメイン名の解析サービス業務に従事すると、どのような影響がありますか。
『中華人民共和国の電気通信規則』 第六十九条によると：（一）本規制の第七条第三項、または本規則の第五十八条第（一）項にリストされる行為のいずれかの規定に違反して、許可せずに電気通信業務に従事、または電気通信事業範囲を超える場合、国務院の情報産業部門、または省、自治区、および直轄市の電気通信管理機関は、機能と権限に応じて修正を命令し、違法所得を没収、違法所得の3倍から5倍の罰金を科します。違法所得がない、または違法な収入が5万元未満の場合、10万元以上100万元未満の罰金が科せられ、状況が深刻な場合、事業を停止するようと命じられます。

