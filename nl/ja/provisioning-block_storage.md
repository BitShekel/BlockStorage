---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-09"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockstorageshort}} の注文

ニーズと希望に応じて、ユーザーがプロビジョンできるストレージには 2 つの異なるタイプがあります。 以下に挙げたのが、{{site.data.keyword.blockstorageshort}} ボリュームの 2 つのオプションです。 

- **エンデュランス**: 事前定義されたパフォーマンス・レベル、およびスナップショットや複製などの機能をフィーチャーしたエンデュランス・ティアをプロビジョンします。 
- **パフォーマンス**: 必要とする 1 秒あたりの入出力操作回数 (IOPS) を割り当てることができる高性能パフォーマンス環境を構築します。

## {{site.data.keyword.blockstorageshort}} 用にエンデュランスを注文する方法

1. [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window} から**「ストレージ」**>**「{{site.data.keyword.blockstorageshort}}」**をクリックするか、または  {{site.data.keyword.BluSoftlayer_full}} カタログから、**「インフラストラクチャー」>「ストレージ」>「{{site.data.keyword.blockstorageshort}}」**をクリックします。
2. 右上隅で、**「{{site.data.keyword.blockstorageshort}} の注文 (Order Block Storage)」**リンクをクリックします。
3. 「ストレージ・タイプの選択 (Select Storage Type)」ドロップダウン・リストから**「エンデュランス」**を選択します。
4. ドロップダウン・リストをクリックして、デプロイメント・**ロケーション** (データ・センター) を選択します。
   - 新規ストレージは、以前に注文されたホストと同じロケーションに追加するようにしてください。
   - 機能が改善された (ドロップダウンに * で示されている) データ・センターを選択した場合は、月次請求と毎時請求のいずれかを選択できます。 
     1. **毎時**請求では、ブロック LUN がアカウントに存在していた時間数は、LUN が削除された時点または請求サイクルの終了時点のいずれか早い方で計算されます。  使用期間が数日ないし 1 カ月未満のストレージには毎時請求が適しています。 毎時請求が選択できるのは、これらの [限られたデータ・センター](new-ibm-block-and-file-storage-location-and-features.html)にプロビジョンされたストレージのみです。 
     2. **月次**請求では、価格は作成日から請求サイクル終了までで案分計算され、即時に請求が行われます。 ブロック LUN が請求サイクルの終了前に削除されても、返金は行われません。  長期間 (1 カ月以上) 保管およびアクセスする必要があるデータを使用する実動ワークロードで使用されるストレージには月次請求が適しています。
     **注**: 改善された機能で更新**されていない**データ・センターにプロビジョンされたストレージには、デフォルトで月次請求タイプが使用されます。
5. ご使用のアプリケーションに必要な IOPS ティアの隣にあるラジオ・ボタンを選択します。
    - *0.25 IOPS/GB* は、入出力負荷が低いワークロード用に設計されています。 これらのワークロードには通常、一定時間非アクティブになっているデータの割合が高いという特徴があります。 アプリケーションの例として、メールボックスの保管や部門レベルのファイル共有が挙げられます。
    - *2 IOPS/GB* は、大部分の一般的な用途のために設計されています。 アプリケーションの例として、Web アプリケーションをバッキングする小規模なデータベースのホスティングや、ハイパーバイザーの仮想マシン・ディスク・イメージが挙げられます。
    - *4 IOPS/GB* は、高負荷ワークロード用に設計されています。 これらのワークロードには通常、一定時間アクティブになっているデータの割合が高いという特徴があります。 アプリケーションの例として、トランザクション・データベースやその他の高いパフォーマンスを必要とするデータベースが挙げられます。
    - *10 IOPS/GB* は、NoSQL データベースや Analytics のデータ処理などで作成される最も厳しいワークロード用に設計されています。  このティアは、[限られたデータ・センター](new-ibm-block-and-file-storage-location-and-features.html)で、プロビジョンされたサイズが 4TB までのストレージに対して使用可能です。
6. **「ストレージ・サイズの選択 (Select Storage Size)」**ドロップダウン・リストをクリックして、目的のストレージ・サイズを選択します。
7. **「スナップショット・スペース・サイズの指定 (Specify Snapshot Space Size)」**ドロップダウン・リストをクリックして、スナップショット・サイズを (使用可能スペースと併せて) 選択します。 スナップショット・スペースの考慮事項および推奨事項については、『[スナップショットの注文](ordering-snapshots.html)』を参照してください。
8. ドロップダウン・リストからご使用の**「OS タイプ (OS Type)」**を選択します。
9. ドロップダウン・リストをクリックして、デプロイメント・ロケーション (データ・センター) を選択します。
10. **「続行」**をクリックします。 月額課金と日割り計算額が表示されます。これが注文の詳細を確認できる最後の機会となります。
11. **「マスター・サービス契約を読みました (I have read the Master Service Agreement)」**チェック・ボックスをクリックして、**「注文する (Place Order)」**ボタンをクリックします。
12. 新規ストレージ割り振りは数分後に使用可能になります。

**注**: デフォルトでは、総計 250 個の {{site.data.keyword.blockstorageshort}} ボリュームをプロビジョンできます。 ご使用のボリュームの数を増やすには、営業担当員にお問い合わせください。 制限の引き上げについては、[ここ](managing-storage-limits.html)を参照してください。

同時許可の制限については、[FAQ](BlockStorageFAQ.html) を参照してください。
 
## Block Storage 用にパフォーマンスを注文する方法

1. [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window} から**「ストレージ」**、**「{{site.data.keyword.blockstorageshort}}」**をクリックするか、または  {{site.data.keyword.BluSoftlayer_full}} カタログから、**「インフラストラクチャー」>「ストレージ」>「{{site.data.keyword.blockstorageshort}}」**をクリックします。
2. 画面の右上隅にある**「{{site.data.keyword.blockstorageshort}} の注文 (Order Block Storage)」**をクリックします。
3. **「ストレージ・タイプの選択 (Select Storage Type)」**ドロップダウン・リストから、**「パフォーマンス」**を選択します。
4. **「ロケーション」**ドロップダウン・リストをクリックして、データ・センターを選択します。
   - 新規ストレージは、以前に注文されたホストと同じロケーションに追加するようにしてください。
   - 機能が改善された (ドロップダウンに * で示されている) データ・センターを選択した場合は、月次請求と毎時請求のいずれかを選択できます。 
     1. **毎時**請求では、ブロック LUN がアカウントに存在していた時間数は、LUN が削除された時点または請求サイクルの終了時点のいずれか早い方で計算されます。  使用期間が数日ないし 1 カ月未満のストレージには毎時請求が適しています。 毎時請求が選択できるのは、これらの [限られたデータ・センター](new-ibm-block-and-file-storage-location-and-features.html)にプロビジョンされたストレージのみです。 
     2. **月次**請求では、価格は作成日から請求サイクル終了までで案分計算され、即時に請求が行われます。 ブロック LUN が請求サイクルの終了前に削除されても、返金は行われません。  長期間 (1 カ月以上) 保管およびアクセスする必要があるデータを使用する実動ワークロードで使用されるストレージには月次請求が適しています。
     **注**: 改善された機能で更新**されていない**データ・センターにプロビジョンされたストレージには、デフォルトで月次請求タイプが使用されます。
5. 適切な **「ストレージ・サイズ」** の横にあるラジオ・ボタンを選択します。
6. **「IOPS の指定 (Specify IOPS)」**フィールドに IOPS を入力します。
7. **「続行」**をクリックします。 月額課金と日割り計算額が表示されます。これが注文の詳細を確認できる最後の機会となります。 注文を変更する場合は、**「戻る」**をクリックします。
8. **「マスター・サービス契約を読みました (I have read the Master Service Agreement)」**チェック・ボックスをクリックして、**「注文する (Place Order)」**ボタンをクリックします。
9. 新規ストレージ割り振りは数分後に使用可能になります。

**注**: デフォルトでは、総計 250 個の {{site.data.keyword.blockstorageshort}} ボリュームをプロビジョンできます。 ご使用のボリュームの数を増やすには、営業担当員にお問い合わせください。 制限の引き上げについては、[ここ](managing-storage-limits.html)を参照してください。

同時許可の制限については、[FAQ](BlockStorageFAQ.html) を参照してください。

## 自分の請求書で自分の {{site.data.keyword.blockstorageshort}} を識別する方法

請求書にはすべての LUN が明細項目として表示されます。エンデュランスは「エンデュランス・ストレージ・サービス」と表示され、パフォーマンス は「パフォーマンス・ストレージ・サービス」と表示されます。価格はご使用のストレージ・レベルによって異なります。 したがって、「エンデュランス」や「パフォーマンス」を展開すれば、それが  {{site.data.keyword.blockstorageshort}} であることが確認できます。
