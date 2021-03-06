インスタンスがシャットダウンする時に課金しないとは、従量課金インスタンスは**シャットダウンする時に課金しない選択肢を選択する**ことによりインスタンスを**シャットダウンした**状態にした後に、インスタンス（CPU、メモリ）に対し課金しないことです。クラウドディスク（システムディスクとデータディスク）、パブリック帯域幅やイメージ等のコンポーネントは引き続き課金されます。

## 利用制限

**インスタンスがシャットダウンする時に課金しない**機能は**システムディスクもデータディスクもクラウドディスク**である**従量課金インスタンス**のみに適用されています。
インスタンスがシャットダウンする時に課金しない機能は下記のケースに**適用されていません**。
- CVM内部でのオン/オフに適用されていません。
- ローカルディスクインスタンスの搭載に適用されていません。
-  GPUやFPGAなどの異種型インスタンスを対応していません。
- 支払い延滞によりシャットダウンされたインスタンスに適用されていません。インスタンスと関連リソースは支払い延滞によりシャットダウンされた後に課金を停止します。コンピューティングリソースとパブリックIPがリリースされます。アカウントの残額がプラスになった場合、すべてのリソースが引き続き課金されます。

一括シャットダウン操作には「シャットダウンする時に課金しない」ことに対応する/対応しないインスタンスが同時に含まれている場合は、
- 「シャットダウンする時に課金しない」ことに対応するインスタンスは、シャットダウンされた後に**CPUとメモリ課金を停止します**。
- 「シャットダウンする時に課金しない」ことに対応しないインスタンスは、シャットダウンされた後に**引き続き正常に課金します**。

## 当該機能を有効にした場合の影響

「シャットダウンする時に課金しない」機能を有効にした場合は、インスタンスがシャットダウンされた後に**下記のような影響があります**。
1. インスタンスの CPU とメモリは**保留されない**ため、インスタンスを再度スタートアップする時に**失敗する可能性があります**。この場合、スタートアップを再度試すか、或いは別の時間で再度スタートアップしてみればよいです。
2. インスタンスにパブリックIPアドレスがアサインされた場合は、シャットダウンされた後に、パブリックIPアドレスは**自動的にリリースされます**。インスタンスがスタートアップされた後に、新しいパブリックIPアドレスがアサインします。プライベートIPアドレスは影響を受けず、そのまま保留されます。
 **パブリックIPアドレスを変更したくない場合は、パブリックIPアドレスをEIPに変換してからインスタンスをシャットダウンすればよいです**。インスタンスにバインディングされた EIPは、シャットダウンされた後に保留されます。再度スタートアップしたらEIPが変わりません、シャットダウンした後にEIP利用料が課金されません。
3. シャットダウンされた期間に、*インスタンスのスタートアップ以外**の操作（例えば、コンフィギュレーション調整、ディスク調整、ネットワーク調整、システムのリインストール、インスタンス再起動、パスワードリセット、更新、名前変更などの関連操作）が対応されていませんので、**操作したい場合はスタットアップしてから実行してください**。
4.コンフィギュレーション調整、ディスク調整、システムのリインストール等ほかのメンテナンス操作を行う時に、インスタンスをシャットダウンする必要があります。シャットダウンされている期間に、「シャットダウンする時に課金しない」ポリシーを**実行しません**。

## 操作ガイド

 [従量課金インスタンスのシャットダウンする時に課金しないことに関する操作ガイド](/document/product/213/19922)をご参照ください。
