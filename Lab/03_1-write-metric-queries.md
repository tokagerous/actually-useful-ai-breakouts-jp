# 3.1. メトリクスクエリを書く

## ログ行からメトリクスを計算する

Loki では、特定の特性を持つログ行の数に基づいてメトリクスを簡単に計算できます。たとえば、エラーログの発生率などです。これは **ログ範囲集計（log range aggregation）** と呼ばれます。

このワークショップのセクションでは、Loki のクエリ時 JSON 解析と、LogQL のメトリクス関数 `count_over_time` および `sum` を使って、ログ量を分析します。

1.  Grafana のメインメニューから **Explore** を選択し、データソースピッカーで **LokiNGINX** データソースを選択します。

2.  **Code** ボタンをクリックして LogQL のコードエディターを表示します。以下のクエリをクエリボックスに貼り付け、**Run query** を押します。

    ```
    {filename="/var/log/nginx/json_access.log"} |= "Googlebot"
    ```

    googlebot のリクエストの JSON ログ行が取得されることが分かります。**ログ行をクリック**して詳細を確認します。

3.  この時点では、Loki はまだ JSON を解析していません。ログ行はプレーンテキストのまま表示されます。ログ行を解析するには、`json` のようなパーサーを追加する必要があります。

    **クエリを次のように変更**し、**Run query** を押します。

    ```
    {filename="/var/log/nginx/json_access.log"} |= "Googlebot" | json
    ```

    では、**ログ行をクリック**して展開します。

    JSON メッセージのフィールドが Loki によって解析され、_Fields_ パネルに表示されるようになったことが分かります。これらのフィールドは、メトリクスクエリで使用できます。

    このスクリーンショットでは、抽出されたフィールドのうち、いくつかをハイライトしています。

    <img width="1255" height="732" alt="image" src="https://github.com/user-attachments/assets/1a979299-a6dc-4a57-beb6-7e85df4b0fae" />

4.  クエリを次のように編集して実行します。

    ```
    sum by(status) (count_over_time({filename="/var/log/nginx/json_access.log"} |= `Googlebot` | json [5m]))
    ```

    これで Grafana は、Googlebot のリクエスト数を 1 分あたりで、（HTTP）ステータスコードごとに分けて表示します。

>[!Tip]
>Loki の LogQL クエリの内容を理解するには、**Explain query** トグルをクリックします。

5.  **+ Add query** ボタンをクリックし、以下のクエリを貼り付けます。これは、NGINX ログ内のログエントリの総数を時系列で計算します。

    ```
    sum by (request_method) (count_over_time({filename="/var/log/nginx/json_access.log"} | json [5m]))
    ```

    Grafana は 2 つのクエリの結果を同じグラフにまとめて表示します。このグラフでは次のことが分かります。

    - 時系列でのリクエストの総数
    - Googlebot から送信されたリクエスト数（HTTP ステータスコードごとの内訳）
    - 全リクエストに対する Googlebot リクエストの割合

    この情報は、ログを事前に解析することなく、Loki によってリアルタイムで抽出されたものです。

## オプション: ログ内の値に基づいてメトリクスを計算する

Loki では、ログ行自体に含まれる値を使ってメトリクスを計算することもできます。たとえば、平均応答時間や、ペイロードサイズの平均を時系列でグラフ化するといったことです。これは **アンラップ範囲集計（unwrapped range aggregation）** と呼ばれます。`unwrap` 関数を使って、ログ行のフィールドを `avg_over_time` や `max_over_time` などのメトリクス関数に渡します。

1. 以下のクエリを実行して、各 JSON ログ行から `bytes_sent` フィールドを抽出します。これにより、GoogleBot が 5 分ごとにリクエストした平均バイト数を示すチャートが描画されます。

    ```
    avg_over_time({filename="/var/log/nginx/json_access.log"} |= "Googlebot" | json | unwrap bytes_sent [5m]) by (host)
    ```

2. **+ Add query** ボタンをクリックして、別のクエリを追加します。

    ```
    max_over_time({filename="/var/log/nginx/json_access.log"} |= "Googlebot" | json | unwrap bytes_sent [5m]) by (host)
    ```
    2 つ目のメトリクス系列がグラフに追加され、その `5m` の間隔内における最大の応答バイト数が返されることが分かります。

>[!NOTE]
>`max_over_time` は、指定された間隔（この例では 5 分）内のすべての値の最大値を計算します。

この演習で完成したグラフには、NGINX が送信した平均バイト数と、同じ期間内に送信された最大のペイロードとの比較が表示されます。
