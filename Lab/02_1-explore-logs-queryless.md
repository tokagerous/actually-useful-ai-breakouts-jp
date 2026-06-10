# 2.1. クエリなしでログを探索する

## サービスのログを表示する

Grafana の _Logs Drilldown_ アプリを使うと、Loki のログを簡単に閲覧・検索・フィルタリングできます。このアプリを使えば、クエリを書かなくても必要なログをすばやく見つけられます。

このラボでは、必要なログをすばやく見つけるのに役立つ Logs Drilldown の基本機能を確認します。一部のユーザーから、Web サイトで favicon が表示されないという報告が寄せられているので、その原因を突き止められるか試してみましょう。

1. 左メニューで **Drilldown** メニュー項目を展開し、**Drilldown** -> **Logs** をクリックします。

    Logs Drilldown アプリがサービス一覧とともに開きます。この一覧には、Loki にログを送信しているすべてのサービスが、`service_name` ラベルごとにグループ化されて表示されます。これにより、調査したいサービスをすばやく見つけられます。

   <img width="1920" height="1350" alt="image" src="https://github.com/user-attachments/assets/faeb5860-8b8d-47df-adab-312830294bee" />

> [!TIP]
> ログがまったく表示されない場合は、画面右上で **LokiCorrelation** データソースが選択されていることを確認してください。

3. `web_app_3` サービスまでスクロールし、**Show logs** ボタンをクリックしてこのサービスのログを開きます。

    <img width="1872" height="300" alt="image" src="https://github.com/user-attachments/assets/2d805c4b-f825-4c8e-8a1b-1c1cfb68fc31" />

4. これで、アプリ **web_app_3** の最新のログが表示されます。このビューには次の情報が表示されます。

    - このサービスが受信したログ量の推移を示すチャート

    - このサービスのログ行の一覧

    - 検索対象の期間（右上に表示）

    <img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/9035b0fa-baf7-4673-8253-ed5700d7126a" />

> [!TIP]
>   ログ行がブラウザの幅に収まらない場合は、横スクロールするか、**Wrap** ボタンをクリックして行の折り返しを有効にできます。

## Loki のログ行を理解する

1. **web_app_3** のログを表示した状態で、**表示されたログ行のいずれかをクリック**して展開します。

2. ログ行を展開すると、詳細ビューにそのログに関連付けられた **Labels and Fields** が表示されます。

    - _Indexed labels_: Loki のインデックス内でログ行の位置を特定するラベル

    - _Structured metadata_: ログ行に付与された、インデックス化されていないキーと値のペア

    - _Parsed labels_: ログ行自体に含まれるフィールドで、Loki がクエリ実行時に解析したもの

    これらのフィールドは、上記の区分ごとに分類されたテーブルに表示されます。

    <img width="2694" height="1685" alt="image" src="https://github.com/user-attachments/assets/6d17618d-75f4-47f4-96f5-10b1f42847d0" />


## 検索とフィルタリング

1. では、404 ステータスコードを返している `favicon.ico` ファイルへのリクエストをすべて見つけて、Web サイトのトラブルシューティングを行いましょう。

    検索バーに **favicon** と入力します。これにより、文字列 `favicon` を含むログ行だけが表示されます。

    <img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/cd499daa-ab6d-402f-a160-25c46a07c9df" />


2. 次に、status_code のフィルターを追加しましょう。

    **ログ行をクリック**して展開します。次に、**status_code** の横にある、プラス記号付きの虫眼鏡アイコンをクリックします。

    <img width="3119" height="725" alt="image" src="https://github.com/user-attachments/assets/e17dfd80-2730-41ef-a7ff-29af9fec1c07" />


3. ログの結果が更新され、文字列 `favicon` を含み、**かつ** いま選択した `status_code` を持つログだけが表示されます。

    ページ上部には、設定したすべての検索フィルターが表示され、簡単に変更・削除できます。

    <img width="1920" height="525" alt="image" src="https://github.com/user-attachments/assets/db29e20d-187b-4501-a3d5-bda793fbec61" />


4. status_code フィルターを変更して、404 のレスポンスだけを表示しましょう。画面上部の **status_code** ラベルフィルターをクリックし、ドロップダウンリストから **404** の値を選択します。

    これで、文字列 `favicon.ico` を含み、status_code が **404** であるアプリのログだけが表示されます。favicon が表示されない謎の解明に、すでに近づいています。

    <img width="1920" height="975" alt="image" src="https://github.com/user-attachments/assets/523a878e-3ea6-450e-b2bf-e6d25b6dbf35" />

## オプション: ログからメトリクスを表示する

Logs Drilldown では、即時メトリクスやチャートを通じて、ログの形状や内容を深く理解することもできます。

内部的には、Loki の _metrics from logs_ 機能がログ行やラベルからメトリクスを即座に計算し、Logs Drilldown がそれをチャートとして可視化します。

これにより、次のようなよくある質問にすばやく答えられます。

- アプリのエラー数は増加しているか？
- アプリで最もアクセスの多いルートや、Web サイトで最も人気のあるページはどれか？

これらのメトリクスを見てみましょう。

1. ログ量チャートの上にあるタブの行で、**Labels** タブをクリックします。

    Logs Drilldown に Loki のラベルの内訳が表示され、それぞれの値がチャート化されます。

    **http_method** パネルで **Select** ボタンをクリックし、このラベルを持つログをドリルダウンします。

    <img width="1915" height="842" alt="image" src="https://github.com/user-attachments/assets/70b43f3d-560d-49be-bb95-7363ace02e7a" />


2. これで、フィルタリングされたログがさらに `http_method` ラベルごとに細分化されて表示されます。

    任意のパネルで **Include** ボタンをクリックすると、そのラベル値を持つログだけが表示されます。

    <img width="1917" height="815" alt="image" src="https://github.com/user-attachments/assets/02b04fef-6f18-492c-a1f5-3c64b9aa55cc" />


3. 次に **Logs** タブをクリックして、ログ一覧に戻ります。

    選択した `http_method` ラベル値を持つログだけが表示されるよう、さらにフィルタリングされていることが分かります。

    <img width="1512" height="765" alt="image" src="https://github.com/user-attachments/assets/a3d527b8-43f2-48c9-9fc6-86a3eb396114" />


## まとめ

Logs Drilldown は、クエリを書かずにログを掘り下げ、即座にインサイトを得るための強力なツールです。

Loki をさらに深く扱いたくなったら、Loki のクエリ言語である _LogQL_ でクエリを書き始められます。このラボの次のセクションでは、Logs Drilldown から Loki のクエリ作成へと進みます。
