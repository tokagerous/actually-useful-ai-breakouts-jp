# データソースを接続する
### Prometheus メトリクスのデータソース
1. 左メニューの **Connections > Data sources** を開き、**Add data source** をクリックします。
2. 以下の設定で新しい Prometheus データソースを追加します。
  - Name: `PromCorrelation`
  - URL を入力: `https://prometheus-prod-56-prod-us-east-2.grafana.net/api/prom`
  - Basic auth のトグルを有効にする
  - Username: `2458181`、Password: `glc_eyJvIjoiNjMxOTY1IiwibiI6Imxva2ktd29ya3Nob3AtYXR0ZW5kZWVzLWxva2ktd29ya3Nob3AtMjAyNWIiLCJrIjoiZEJiNDJqMGFNOTI1TTdPQVYxYzlXQ000IiwibSI6eyJyIjoicHJvZC11cy1lYXN0LTAifX0=` を入力
3. **Save and Test** をクリックします。
### Tempo トレーシングのデータソース
1. 以下の設定で新しい Tempo データソースを追加します。
  - Name: `TempoCorrelation`
  - URL を入力: `https://tempo-prod-26-prod-us-east-2.grafana.net/tempo`
  - Basic auth のトグルを有効にする
  - Username: `1219078`、Password: `glc_eyJvIjoiNjMxOTY1IiwibiI6Imxva2ktd29ya3Nob3AtYXR0ZW5kZWVzLWxva2ktd29ya3Nob3AtMjAyNWIiLCJrIjoiZEJiNDJqMGFNOTI1TTdPQVYxYzlXQ000IiwibSI6eyJyIjoicHJvZC11cy1lYXN0LTAifX0=` を入力
2. **Save and Test** をクリックします。
### Loki ログのデータソース
1. 以下の設定で新しい Loki データソースを追加します。
  - Name: `LokiCorrelation`
  - URL を入力: `https://logs-prod-036.grafana.net`
  - Basic auth のトグルを有効にする
  - Username: `1224767`、Password: `glc_eyJvIjoiNjMxOTY1IiwibiI6Imxva2ktd29ya3Nob3AtYXR0ZW5kZWVzLWxva2ktd29ya3Nob3AtMjAyNWIiLCJrIjoiZEJiNDJqMGFNOTI1TTdPQVYxYzlXQ000IiwibSI6eyJyIjoicHJvZC11cy1lYXN0LTAifX0=` を入力
  -  Derived fields セクションで Add をクリックし、以下の項目を入力します。
      - Name: TraceId
      - Regex: `.*tempo_trace_id".*?"(.*?)".*`
      - URL: `${__value.raw}`
      - 内部リンク（internal link）を有効にし、TempoCorrelation データソースを選択する
3. **Save and Test** をクリックします。
### Loki NGINX のデータソース
1. 以下の設定で新しい Loki データソースを追加します。
  - Name: `LokiNGINX`
  - URL を入力: `https://logs-prod-036.grafana.net`
  - Basic auth のトグルを有効にする
  - Username: `1224767`、Password: `glc_eyJvIjoiNjMxOTY1IiwibiI6Imxva2ktd29ya3Nob3AtYXR0ZW5kZWVzLWxva2ktd29ya3Nob3AtMjAyNWIiLCJrIjoiZEJiNDJqMGFNOTI1TTdPQVYxYzlXQ000IiwibSI6eyJyIjoicHJvZC11cy1lYXN0LTAifX0=` を入力
2. **Save and Test** をクリックします。
