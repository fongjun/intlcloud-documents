完全なASソリューションを作成するには、次の3つのステップで完了する必要があります：
![](https://main.qcloudimg.com/raw/05ef734d160c1c73061cf103cfb44d91.png)

>? 次の例の操作で使用されるコンソールモードで、APIモードを使用したい場合は、[APIの使用例](https://cloud.tencent.com/document/product/377/4232)を参照してください。

## 起動構成の作成
起動構成は、AS用のCVMインスタンスの構成情報を定義します。CVMのイメージ、ストレージ、ネットワーク、セキュリティグループ、ログイン方法、その他の構成情報が含まれています。

>? 構成起動サービスは**完全無料**です。安心に作成してください。

1. [ASコンソール](https://console.cloud.tencent.com/autoscaling/config)にログインします。
2. 左側のナビゲーションバーで［起動構成］をクリックすると、起動構成管理ページに入ります。

### エリアの選択

1. 以下の図のように、起動構成のプロジェクトと地域を選択します：
エリアを選択すると、手動で追加できるCVMとバインディングできるCLBが制限されます。例えば、構成起動のエリアは広州を選択すれば、スケーリンググループに自動的に追加されるのは広州のCVMです。エリアは広州であるスケーリンググループの中に、上海、北京、香港、トロントなど他のエリアのCVMを手動で追加することができなく、上海、北京、香港、トロントなど他のエリアのCLBもバインディングできません。
![地域の選択](https://main.qcloudimg.com/raw/07a746c4f69b1a8641b74ce1ddc5cf4f.png)
2. ![](//mccdn.qcloud.com/static/img/9d38f7bfbe02a922370765f3adfa58bf/image.png)をクリックして、ポップアップページで起動構成の基本情報を入力します。

### モデルの選択

「起動構成の作成」ページで、起動構成名を入力し、Availability Zoneを選択して、モデルを選択します。以下の通りです。
![](https://main.qcloudimg.com/raw/a38b03bb3c23c1393169598fcb195897.png)
- 構成名称を付けます。
- スケーリンググループがバインディングするCVMと同じモデルを選択します。

### イメージの選択

起動構成を作成するには、パブリックイメージまたはカスタムイメージを使用することができます。
環境を構成したカスタムイメージを使用することをお勧めします。その理由は以下の通りです：
- パブリックイメージを選択した場合、スケーリングされたCVMインスタンスは元のOSなので、アプリケーション環境を手動で構成する必要があります。
- カスタムイメージを選択した場合は、環境を構成したCVMインスタンスに対してイメージを作成して、そのイメージを使用してCVMインスタンスを一括作成して、CVMインスタンスが正常に作成されると、以前のCVMインスタンスと同じソフトウェア環境があります。以上によって、一括構成する目的を達することができます。

「スケーリンググループがバインディングするCVM」のイメージをバインディングします。「スケーリンググループがバインディングするCVM」のイメージを作成するには、[カスタムイメージの作成](https://cloud.tencent.com/document/product/213/4942)を参照してください。
![](https://main.qcloudimg.com/raw/9459776ded2df48d711cf4b6a2bb77a4.png)

### ストレージと帯域幅の選択

「3.ストレージと帯域幅の選択」ページで、ディスクとネットワークを設定します。以下の通りです。
![](https://main.qcloudimg.com/raw/c958b41819de424b6e4b7e414c92d71e.png)
>! システムディスクがクラウドディスクを選択した場合、データディスクはデータディスクスナップショットを選択できます。

大量のデータを持つユーザーにとって、データディスクがデータの保存によく使用されます。データディスクAでスナップショットファイルが作成されると、ユーザーはスナップショットファイルを使用して複数のディスクをすばやくコピーし、サーバーを迅速に配置する目的を果たします。

ASは新しいCVMインスタンを自動的に追加するときに、起動構成データディスクがデータディスクスナップショットを指定した場合、Tencent Cloudディスクは、CVMインスタンスの起動後に自動的に設定データを含むデーターディスクをマウントすることをサポートできます。これによって自動データコピーのニーズを満たします。

起動構成でデータディスクスナップショットが指定されている場合、データディスクは自動的に正しくマウントされることを保証しなと、スケーリンググループは自動的に拡張されません。新しいCVMインスタンスを起動する時にデータディスクの自動マウントをサポートするように、ASを設定する前に、データディスクスナップショットの元のインスタンスに対していくつかの操作を実行する必要があります。具体的な方法について、[カスタムイメージとデータディスクスナップショットを使用して新しいインスタンスを起動するときにデータディスクを自動的にマウントする方法](https://cloud.tencent.com/doc/product/362/5564)を参照してください。

>?  ASサービスは無料です。新しいサーバー、ディスク、およびネットワークは、CVMインスタンス、ディスク、およびネットワークに従って、従量制課金されます。このページでは、設定に基づいて、価格が表示されます。

### 情報の設定

1. 「4.セキュリティグループとCVMの設定」ページで、ログイン方法、セキュリティグループを選択します。ASサービスによって新しく追加されたCVMインスタンスはデフォルトで無料でクラウドセキュリティとCMサービスを利用します。以下の図のように：
![](https://main.qcloudimg.com/raw/e7e5cba6e2c52b768e959a5db4c73bae.png)
2. 構成が完了すると、このアイテムはページの起動構成リストに表示されます。以下の図のように：
![](https://mc.qcloudimg.com/static/img/67ba31fd6c1f12485bb8f96220aaf6af/image.png)

## スケーリンググループの作成

スケーリンググループは、同じ規則に従い、同じシナリオに向けるCVMインスタンスのセットです。
1. [ASコンソール](https://console.cloud.tencent.com/autoscaling/config)にログインします。
2. 左側のナビゲーションバーで【スケーリンググループ】をクリックすると、スケーリンググループ管理ページに入ります。

### スケーリンググループの新規作成

1. ![](//mccdn.qcloud.com/static/img/9d38f7bfbe02a922370765f3adfa58bf/image.png)をクリックして、ポップアップページでスケーリンググループ基本情報を入力します。その中で、![](//mccdn.qcloud.com/static/img/f9df27a1d1e0d42a7ff08dd884bfa34c/image.png)は必須項目です。以下の図のように：
![](https://mc.qcloudimg.com/static/img/2fb365611291fb8917637dba46f398f4/image.png)
 - スケーリンググループの現在のCVMインスタンス数は、最小スケーリング数と最大スケーリング数の間で維持されます。
	- 開始インスタンス数は、スケーリンググループの最初のCVMインスタンス数を定義します。
	- 現在のCVMインスタンス数が最小スケーリング数より少ない場合、最小スケーリング数に等しくするため、ASサービスは自動的にインスタンスを追加します。
	- 現在のCVMインスタンス数が最大スケーリング数より大きい場合、最大スケーリング数に等しくするため、ASサービスは自動的にインスタンスを削除します。
 - 既存の起動構成を選択して、起動構成を新規作成することもできます。
 - ネットワーク、Availability Zone、除去ポリシーを選択します。
 - （オプション）既存CLBポリシーを関連付けるか、CLBを新規作成するかを選択します。
2. 構成が完了すると、このアイテムはページのスケーリンググループリストに表示されます。以下の図のように：
![](https://mc.qcloudimg.com/static/img/c1c64cdb16c11aaa6d31bc4781db62c4/image.png)

### CVMの追加（オプション）

CVMリストにバインディングするCVMインスタンスを追加します。構成が完了すると、このアイテムはページの起動構成リストに表示されます。例は次のとおりです：
![](https://mc.qcloudimg.com/static/img/e3232872ad5fe19e89c9eb7306418a3d/image.png)
>? CVMを追加できない、または除去できない状況が発生している場合は、設定した最大スケーリング数と最小スケーリング数を確認してください。

## スケーリングポリシーの作成

ASグループは、スケーリングポリシーに従ってCVM数を増減します：
- スケーリングアクティビティーを定時的に実行する用のため、**時限タスク**を作成します。または周期的に実行するかどうかを設定できます。
- **アラームトリガポリシー**を作成します。CMメトリック（CPU、メモリ使用量など）に基づいてスケーリングアクティビティーを実行します。

### 時限タスクを作成します

負荷の変化が予測できる場合は、時限タスクを設定してデバイスの拡張アクティビティを計画することができます。この機能を使用してCVMインスタンスを定期的および周期的に自動的に増減して、業務負荷の変化に柔軟に対応できます。機器の利用率を向上させ、配置とインスタンスのコストを削減します。

1. 【スケーリンググループ】ページで「スケーリンググループ」をクリックしてスケーリンググループ管理ページに入ります。
![スケーリンググループ](https://main.qcloudimg.com/raw/d6e81e4df05c1c8e77368c50b765a55a.png)
2. 【時限タスク】タグを選択して、【新規作成】をクリックします。
![時限タスク](https://main.qcloudimg.com/raw/9ed7c9dbfc82035a82136f5f215cc12a.png)
3. 新規ページで、時限タスク名、実行時間および実行アクティビティーなどの情報を指定します。定時にタスクを実行するために、【重複】をチェックして周期を定義することができます。
![時限タスクの新規作成](https://main.qcloudimg.com/raw/5ebba7a45ab3db576eb3d8fd92246cfe.png)
4. 設定が完了した後、該当時限タスクはページのリストに表示されます。例は以下のとおりです：
![時限タスクリスト](https://main.qcloudimg.com/raw/f21339e4d6650929e4b69ff61ce371e5.png)

### アラームトリガポリシーの作成

CVMメトリック状況に基づいて業務配置を調整したい場合は、アラームトリガーポリシーをカスタマイズできます。業務負荷によってメトリックがしきい値に達すると、このポリシーは自動的にCVMインスタンスの数を増減し、業務負荷の変化に柔軟に対応して、デバイス使用率を向上させ、配置コストとインスタンスコストを節約します。

>?
> - 不健康CVMを置き換えるために、スケーリンググループを作成するとき、ディフォルトでpingが到達できないアラームトリガポリシーを作成します。
> - アラームトリガポリシーを使用する前に、CVMのイメージに新しいバージョンのCM Agentをインストールする必要があります。インストール方法については、[監視コンポーネントのインストール](/doc/product/248/監視コンポーネントのインストール)を参照します。

1. 【スケーリンググループ】ページでスケーリンググループIDをクリックしてスケーリンググループ管理ページに入ります。
![スケーリンググループ](https://main.qcloudimg.com/raw/d6e81e4df05c1c8e77368c50b765a55a.png)
2. 【アラームトリガポリシー】タブを選択して、【新規作成】をクリックします。
![](https://main.qcloudimg.com/raw/2fac8567b4042a2c65c1906ae8f8396d.png)
3. 新規ページで、アラームポリシーを設定します。CMのパフォーマンスメトリック（CPU、メモリ、帯域幅など）に基づいて、スケーリンググループに指定台数または指定割合のCVMインスタンスを自動的に増減します。
ポリシーのコピー（オプション）によって、既存のスケーリンググループの既存のポリシーを現在のスケーリンググループにコピーすることもできます。
![アラームトリガポリシーの新規作成](https://main.qcloudimg.com/raw/41c7c0f95256e5b8492dc58826d13cd4.png)
4. 設定が完了した後、該当アラームトリガポリシーはページのリストに表示されます。例は以下のとおりです：
![アラームポリシーリスト](https://main.qcloudimg.com/raw/3b2af877848e11c337901172055ba466.png)



