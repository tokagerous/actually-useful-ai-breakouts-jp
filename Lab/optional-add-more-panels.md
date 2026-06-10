# オプション: ダッシュボードにさらにパネルを追加する

>[!NOTE]
>最初のダッシュボードや Geomap パネルを作成する手順については、[03_2-create-dashboard.md](/Lab/03_2-create-dashboard.md) を参照してください。

## 95 パーセンタイルのパネルを追加する

次に、リクエスト時間の 95 パーセンタイルを表示するパネルを追加します。

1. **Add Visualization** ボタンをクリックします。

2. **LokiNGINX** データソースを選択します。

3. 以下のクエリを追加します。これは、各ログ行から _request time_ を抽出し、その値の 95 パーセンタイルを計算します。

    ```
    quantile_over_time(0.95,{filename="/var/log/nginx/json_access.log"} 
        | json 
        | upstream_cache_status="MISS" 
        | unwrap request_time 
        |  __error__=""  [5m]) by (host)
    ```

    このクエリにより、キャッシュから提供されなかったリクエストの、ほぼ最悪ケースに近いパフォーマンスを、ホストごとに把握できます。

    具体的には、_95 パーセンタイル_ とは、リクエストの 95% がこの値より短い応答時間であり、長くかかるのは残りの 5% だけということを意味します。パーセンタイルを使うと、まれな極端値に左右されずに最も遅いリクエストを把握できるため、キャッシュなしコンテンツの応答時間におけるパフォーマンスの問題や異常なパターンを検出するのに役立ちます。

4. **+ Add query** をクリックして、このパネルに 2 つ目のクエリを追加します。これは、1 分間隔ごとの最大リクエスト時間を表示します。

    ```
    max_over_time({filename="/var/log/nginx/json_access.log"} 
        | json 
        | upstream_cache_status="MISS" 
        | unwrap request_time 
        |  __error__=""  [1m]) by (host)
    ```

5. 各クエリの下にある **Options** パネルをクリックし、次のように設定します。

    - 95 パーセンタイルのクエリの **Legend** の値を `{{host}} - 95%` に設定する

    - max_over_time のクエリの **Legend** の値を `{{host}} - max` に設定する

>[!NOTE]
>`{{host}}` というプレースホルダーは、Loki のメトリクスクエリ結果から `host` ラベルを挿入するよう Grafana に指示するものです。


6.  **Panel options** サイドバーで、パネルのタイトルを **95th percentile of Request Time** に設定し、**Back to dashboard** ボタンをクリックします。

7.  **Save dashboard** をクリックして、ここまでの内容を保存しましょう。
  
## Googlebot によるリクエストの割合を示すパネルを追加する

次に、Google の Web スパイダーである Googlebot によって行われたリクエストの割合を表示するパネルを追加します。

1. 右上隅から **Add** -> **Visualization** をクリックします。

2. **LokiNGINX** データソースを選択します。

3. 以下のクエリを追加します。ここでは Loki のメトリクスで計算を行っている点に注目してください。この例では、10 分間隔ごとに、任意のブラウザ（`Mozilla`）からのリクエストに対する Googlebot からのリクエストの割合を計算しています。

    ```
    sum(rate(({filename="/var/log/nginx/json_access.log"} 
        |= "Googlebot")[10m])) 
    / 
    (sum(rate(({filename="/var/log/nginx/json_access.log"} |= "Mozilla")[10m])) / 100)
    ```

4. これを 1 つの数値として表示したいので、右側のパネル設定の上部で、Visualization を **Stat** に変更します。

5. Stat パネルは、結果から Grafana が計算した大きな太字の数値を表示します。現在の割合を表示したいので、**Value options** までスクロールし、**Calculation** フィールドで **Last** が選択されていることを確認します。

6. **Standard Options** の **Unit** ドロップダウンで **Misc -> Percent (0-100)** を選択し、単位をパーセントに設定します。

8. パネルのタイトルを **Current % of request by Google** に設定し、**Back to dashboard** をクリックします。

9.  **Save dashboard** をクリックしてダッシュボードを保存します。

## ログ行の書き換え

Loki では、クエリ実行中にログデータを変換できます。ログ行には `line_format` を、ラベルには `label_format` を使用します。これにより、ログデータをその場で整形し直し、特定の情報を抽出したり、ダッシュボードでより分かりやすい形に再フォーマットしたりできます。

ドキュメントはこちらで確認できます: https://grafana.com/docs/loki/latest/query/log_queries/#line-format-expression

Loki クエリの結果を再フォーマットして、ダッシュボードで可視化してみましょう。

1.  ダッシュボードから **Add**、次に **Visualization** をクリックします。

2.  **LokiNGINX** データソースを選択します。

3.  サイドバー上部のドロップダウンで、**Logs** ビジュアライゼーションを選択します。

1.  以下のクエリをクエリボックスに追加します。

    ```
    {filename="/var/log/nginx/json_access.log"} | json | line_format "🚀 request for {{.request_uri}} with HTTP status: {{.status}} ✌️"
    ```

    ここで行っていることに注目してください。
    - `json` パーサーを使って、クエリ実行時に JSON から値を抽出する
    - `line_format` を使ってログ行を書き換える
    - `{{.my_field}}` という構文で JSON フィールドを参照する

4.  パネルのタイトルを **Logs** に変更し、ダッシュボードに戻ります。

5.  作業を保存します。
