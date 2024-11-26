# kbrc-api-specification

見分録 Cloud の REST_API 仕様書を管理します。

## 各ファイルの説明

```
.
├── components
│   └── schemas
│       ├── accounts.yml
│       ├── accumulations.yml
│       ├── alarms.yml
│       ...
├── kbrc-api.yaml
├── paths
│   ├── accounts.yml
│   ├── accumulations.yml
│   ├── alarms.yml
│   ...
└── 見分録Cloud_REST_API仕様書_v1.1.0.html
```

### kbrc-api.yaml

[OpenAPI](https://spec.openapis.org/oas/latest.html) 仕様に則った API 仕様書です。


### 見分録Cloud_REST_API仕様書.html

[redocly cli](https://redocly.com/redocly-cli/) によって生成される HTML 形式の API 仕様書です。

ローカルにダウンロードし、ブラウザで開くことにより閲覧が可能です。


## ファイル構成

機能ごとにファイルを作成します。

* ダッシュボード機能 `dashboards` API の場合

### kbrc-api.yaml の記法の例

メソッド毎に行を作成します。

```yml
paths:
  # ダッシュボード
  "/dashboards/alarm":
    $ref: "./paths/dashboards.yml#/dashboards~1alarm"
  "/dashboards/device":
    $ref: "./paths/dashboards.yml#/dashboards~1device"
```

> `/` はパスとして認識されるため、文字列で扱う場合は `~1` で置換します。

### paths/dashboards.yml

kbrc-api.yaml における `"./paths/dashboards.yml#/dashboards~1alarm"` に対応するオブジェクトを定義します。

```yml
"dashboards/alarm":
  get:
    summary: 警報状態取得
    description: 警報状態を取得する。
    security:
      - bearerAuth: []
    tags:
      - ダッシュボード
    parameters:
      - in: query
        name: mode
        required: false
        description: >
          モード
            * 1 - デマンド
            * 2 - しきい値
            * 3 - デマンド | しきい値
        schema:
          type: integer
          enum:
            - 1
            - 2
            - 3
          default: 3
          example: 2
    responses:
      "200":
        description: 正常終了
        content:
          "application/json":
            schema:
              $ref: "../components/schemas/dashboards.yml#/alarmSummaryResult"
      "400":
        description: パラメータエラー
        content:
          "application/json":
            schema:
              $ref: "../components/schemas/errors.yml#/error"
    ...
```

### components/schemas/dashbords.yml

paths/dashboards.yml における `../components/schemas/dashboards.yml#/alarmSummaryResult` に対応します。

```yml
alarmSummaryResult:
  type: object
  description: 警報状態取得レスポンス
  properties:
    demand:
      type: object
      required:
        - attention
        - block
        - excess
      properties:
        attention:
        ...
```


## HTML 形式での出力

以下の手順により HTML 形式に変換が可能です。

依存ライブラリをインストールします。

```sh
npm install
```

以下を適当なファイル名を指定して実行します。

```sh
npx redocly build-docs {API仕様書}.yaml --output {出力ファイル名}.html
```

コマンド例


```sh
npx redocly build-docs kbrc-api.yaml --output 見分録Cloud_REST_API仕様書_v1.1.0.html
```

## API 定義更新時の取り扱い

以下の方針とします。

* HTML ファイルは直接編集しない
* YAML ファイルを更新したときは `redocly build-docs` コマンドで HTML ファイルも更新する
