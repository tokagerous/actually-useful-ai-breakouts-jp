# 3.2. Loki でダッシュボードを作成する

## 新しいダッシュボードを保存する

ラベル抽出クエリとメトリクス抽出クエリを使って、最初の Loki ダッシュボードを作成しましょう。

1. Grafana 右上のプラスボタンをクリックして、新しいダッシュボードを作成します。

2. 時間範囲ピッカー（右上）で **last 3 hours** を選択します。

3. **Save dashboard** をクリックし、ダッシュボードに名前を付けます。

## Geomap パネルを追加する
次に、IP アドレスのジオコーディングによって付与された国コードを使って、Geomap を表示するパネルを追加します。

1. ダッシュボードが編集モードになっていることを確認します。（Grafana 11 以降では、右上隅の **Edit** ボタンをクリックする必要があります。）

2. **Add** -> **New visualization** をクリックします。

3. **LokiNGINX** データソースを選択します。

4. 以下のクエリを貼り付けます。これは、抽出された country_code ごとにグループ化してログ行をカウントします。

    ```
    sum by (geoip_country_code) (count_over_time({filename="/var/log/nginx/json_access.log"} | json | __error__="" [1m]))
    ```

5.  クエリボックスの下にある **Options** をクリックしてオプションパネルを展開し、**Legend** の値を `{{geoip_country_code}}` に設定します。

6.  クエリの上にある **Transformations** タブをクリックし、**+ Show more** をクリックします。

7.  トランスフォーメーションのパレットから **Reduce** トランスフォーメーションを追加し、**Series to rows** モードを選択します。**Calculations** フィールドには **Total** を選択します。

8.  サイドバー上部の **All visualizations** をクリックし、パネルタイプとして **Geomap** を検索します。

9.  **Panel options** サイドバーで、以下の設定を変更します。

    - **Map layers** で、**Layer type** を **ArcGIS MapServer** に変更します。
    - **Add layer** ボタンをクリックし、**Markers** タイプの新しいレイヤーを追加します。これにより、既存のレイヤーの上に新しいレイヤーが追加されます。可視化のためには、ArcGIS MapServer レイヤーが Markers の下に表示されることが重要です。
    - 新しく追加した **Markers** レイヤーをクリックし、次のように設定します。
        - **Location Mode** を **Auto** から **Lookup** に変更する
        - **Lookup field** を **Field** に変更する
        - **Gazetteer** フィールドが **Countries** になっていることを確認する
        - **Styles Size** フィールドが **Total** で、Min が 10、Max が 40 になっていることを確認する
        - **Color** が **Fixed Color** で、**red** の色が選択されていることを確認する
        - **Fill opacity** を **0.8** に設定する

    - パネルのタイトルを **Total requests per country** に変更します。

10.  ダッシュボードに戻り、ここまでの作業を **save** します。

## さらにパネルを追加する
他のパネルタイプを追加する手順については、オプションのラボを参照してください: [optional-add-more-panels.md](/Lab/optional-add-more-panels.md)
