# オプション: メトリクス、ログ、トレースを相関させる

> [!NOTE]
> Grafana インスタンスに Prometheus と Tempo のデータソースが追加されていることを確認してください。[01-connect-data-sources.md](/Lab/01-connect-data-sources.md) を参照してください。

Loki のラベルは Prometheus のラベルと同じように機能し、ログデータを効率的にクエリ・フィルタリング・整理できます。Grafana では、これらのラベルを使ってログとメトリクスを相関させることができ、関連するデータを並べて表示して、システムの挙動やパフォーマンスについてより深いインサイトを得られます。

1.  Grafana のメインメニューから **Explore** を選択し、データソースピッカーで **PromCorrelation** データソースを選択します。

    <img width="1368" height="932" alt="image" src="https://github.com/user-attachments/assets/cd3f357e-2562-42bf-a6e4-c78e2130dd0d" />

2. **Metrics browser** ボタンをクリックします。次に、Metrics Browser の **Select a metric** で **web** と入力し、メトリクスの一覧から **web_http_requests** を選択します。

    その後、**Use query** をクリックします。

3. 時間範囲ピッカー（右上隅の時計アイコン）が **last 5 minutes** に設定されていることを確認します。

    3 つの系列に注目してください。各系列は、同時接続ユーザー数の推移をチャート化したものです。

4. クエリにラベルフィルターを追加して、調査対象のサービス `web_app_3` に絞り込みます。

    ```
    web_http_requests{service="web_app_3"}
    ```

    その後、**Run query** をクリックするか、Shift+Enter を押して実行します。

    「のこぎり歯状のパターン」が見えていますか？同時接続ユーザー数が急減している原因を調べてみましょう。

## Prometheus メトリクスと Loki ログを相関させる

1. 時間範囲ピッカーの横にある split ボタンをクリックします。

    <img width="2406" height="1516" alt="image" src="https://github.com/user-attachments/assets/d2fffa5d-95df-414d-9f61-445fdca1256f" />

2. 新しく追加された（右側の）パネルには、同じ Prometheus クエリが表示されています。右側パネルのデータソースを **LokiCorrelation** に変更します。

    Grafana が Prometheus のラベル選択を認識し、Loki のログクエリにも同じラベルを自動で適用するため、関連するログがすぐに表示されます。

3. 時間範囲ピッカーの横にあるチェーンボタンをクリックして、両方のビューの時間範囲を同期します。すでに選択されている場合は、オレンジ色でハイライトされます。

   <img width="1558" height="483" alt="image" src="https://github.com/user-attachments/assets/4c5124ef-df7f-4419-afdf-f7008e934ba9" />

  
4. 左側の Prometheus パネルで、マウスをクリックしてドラッグし、急減が発生した時間範囲を選択します。

   <img width="1070" height="844" alt="image" src="https://github.com/user-attachments/assets/75b1d709-489e-4914-90c2-38ada8646441" />


    Loki パネルの時間範囲が連動し、選択した期間のログが再表示されます。

5. ログ行が多すぎる場合は、Loki クエリを次のように変更して、エラーだけを表示するフィルターを追加します。

    ```
    {service="web_app_3", error_level="ERROR"}
    ```

    "out of memory" を示すログ行が表示されるはずです。これは、同時接続ユーザー数の急減の原因がメモリリークであることを示唆しています。

    ```
    [ERROR] out of memory error. Dying...argh
    ```

## Loki ログとトレースを相関させる

障害だけでなく、レイテンシーの問題もよく発生します。トレースを使えば、複雑なリクエスト/レスポンスのフローの中からボトルネックを見つけられます。

1. `tempo_trace_id` で始まるログ行が表示されるまで、時間範囲をズームアウトします。

    ズームアウトするには、虫眼鏡ボタンを使えます。

2. **Trace ID** を含むログ行をクリックします。

3. 解析されたフィールドの中で、Trace ID の値の横にある青い **Tempo** または **View traces** ボタンをクリックします。

    <img width="1700" height="1044" alt="image" src="https://github.com/user-attachments/assets/f55b5dfd-6c8a-45f3-9af3-e273a0eeebeb" />

4. Grafana がそのトレースの Tempo トレースビューを開きます。

    Grafana の Trace View でトレースを調べ、サービスのリクエスト/レスポンスのフローにおける、起こり得るボトルネックを把握できます。
