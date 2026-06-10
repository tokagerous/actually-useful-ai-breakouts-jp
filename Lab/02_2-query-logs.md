# 2.2. ログをクエリする

それでは、LogQL を使って Loki のクエリを実際に書いてみましょう。

## Loki クエリを実行し、テーブルでログを表示する

1.  メインのログパネルでコンテキストメニュー（パネル右上、_Logs / Table / JSON_ トグルの近くにある 3 つの点）を開き、**Explore** をクリックします。

    <img width="1284" height="383" alt="image" src="https://github.com/user-attachments/assets/4bbdc5c3-f1fb-4401-b577-fa1210227054" />


    Grafana は Logs Drilldown のセッションを自動的に Explore セッションに変換し、LogQL クエリも自動入力されるので、ログのより詳細な分析をすぐに始められます。

    次の点に注目してください。

    - 検索に使われていた LogQL クエリ

    - 現在の期間におけるログ量

> [!Tip]
> LogQL は Loki のクエリ言語です。基本的な LogQL クエリは次のような形になります。
>```
> {my_label="value"} |= `foo`
>```


2.  以下のように LogQL クエリを変更して `status_code` と `http_method` のラベルフィルターを削除します。その後、**Run query** をクリックします。

    ```
    {service_name=`web_app_3`} | json |~ `(?i)favicon\.ico`
    ```

    _ラベルフィルター_（`{label="value"}`）は、あらゆる LogQL クエリの基本構成要素です。これにより、Loki はログのストリームを見つけられます。

    この新しいクエリは次の処理を行います。

    - `service_name` ラベルが `web_app_3` に等しいログをすべて検索する。

    - 各ログ行を解析して JSON フィールドを抽出する。

    - 文字列 `favicon.ico` を含む行をフィルタリングする。

3.  メインの **Logs** パネルで、まだ選択されていなければ、右隅の **Table** トグルをクリックします。

    Table ビューを使うと、ログを表形式で表示でき、確認や分析が容易になります。

    ログから検出されたラベルやフィールドは、左側の _Fields_ パネルに表示されます。

4.  **Fields** パネルで、**status_code** と **http_method** のチェックボックスをオンにします。

    これで、この 2 つのフィールドの列が表示され、ログをより簡単に理解できます。_Line_ 列には元のログ行が表示されます。

    <img width="1546" height="747" alt="image" src="https://github.com/user-attachments/assets/c7438109-53a6-4812-a36c-8d368371fd24" />

>[!Tip]
>ここから、**Download** ボタンをクリックすると、ログをテキスト、JSON、CSV 形式でダウンロードすることもできます。

このように、Loki のクエリ時 JSON パーサーを使えば、ログの内容を事前にインデックス化することなく、クエリ実行時にデータを整形・探索できます。
