# shiori3.dic / optional.dic ユーティリティ関数・変数 利用マニュアル

`yaya_base/shiori3.dic` および `yaya_base/optional.dic` が提供するユーザー向けの関数・変数・疑似イベントをまとめたリファレンスです。

---

## 目次

### shiori3.dic

1. [利用可能なグローバル変数](#1-利用可能なグローバル変数)
2. [ユーザー設定変数](#2-ユーザー設定変数)
3. [SAORI 関連関数](#3-saori-関連関数)
4. [ベースウェアプロパティ関数](#4-ベースウェアプロパティ関数)
5. [チェイン制御関数・構文](#5-チェイン制御関数構文)
6. [遅延イベント関数](#6-遅延イベント関数)
7. [レスポンスヘッダ追加関数](#7-レスポンスヘッダ追加関数)
8. [テキスト処理関数](#8-テキスト処理関数)
9. [話者状態確認関数](#9-話者状態確認関数)
10. [インストール済みゴースト関連関数・変数](#10-インストール済みゴースト関連関数変数)
11. [変数管理ユーティリティ](#11-変数管理ユーティリティ)
12. [配列ユーティリティ関数](#12-配列ユーティリティ関数)
13. [疑似イベント](#13-疑似イベント)
14. [付録：config.dic の主要設定](#付録configdic-の主要設定)

### optional.dic

15. [タグエスケープ関数](#15-タグエスケープ関数)
16. [文字列整形関数](#16-文字列整形関数)
17. [バルーン初期化関数](#17-バルーン初期化関数)
18. [FMO 関連関数・変数](#18-fmo-関連関数変数)
19. [その他ユーティリティ](#19-その他ユーティリティ)

---

## 1. 利用可能なグローバル変数

これらの変数はシステムが自動的に更新します。参照専用として利用してください。

### ゴースト・シェル情報

| 変数名 | 内容 |
|--------|------|
| `SHIORI3FW.GhostName` | 現在のゴースト名 |
| `SHIORI3FW.ShellName` | 現在のシェル名 |
| `SHIORI3FW.ShellPath` | 現在のシェルパス |
| `SHIORI3FW.BalloonName` | 現在のバルーン名 |
| `SHIORI3FW.BalloonPath` | 現在のバルーンパス |
| `selfname` / `sakuraname` | さくら（スコープ0）キャラ名 |
| `keroname` | ケロ（スコープ1）キャラ名 |

### ウィンドウ情報

| 変数名 | 内容 |
|--------|------|
| `SHIORI3FW.HWnd` | キャラウィンドウのHWND配列。`[0]`がさくら側 |
| `SHIORI3FW.BalloonHWnd` | バルーンウィンドウのHWND配列。`[0]`がさくら側 |
| `SHIORI3FW.LastSurface` | 直前に表示していたサーフィス番号配列。`[0]`がさくら側 |
| `SHIORI3FW.IsVisible` | サーフィス表示状態 (1=表示中 / 0=非表示)。`[0]`がさくら側 |

### ユーザー情報

| 変数名 | 内容 |
|--------|------|
| `SHIORI3FW.UserName` | ユーザー名（短縮） |
| `SHIORI3FW.UserNameFull` | ユーザー名（フルネーム） |
| `SHIORI3FW.UserBirthday` | 誕生日配列 `[年, 月, 日]`（整数型） |
| `SHIORI3FW.UserSex` | 性別 |

### トーク履歴

| 変数名 | 内容 |
|--------|------|
| `SHIORI3FW.LastTalk` | 直前に喋ったスクリプト（全イベント対象） |
| `SHIORI3FW.LastAITalk` | 直前のランダムトークスクリプト |

### その他

| 変数名 | 内容 |
|--------|------|
| `SHIORI3FW.Eventid` | 現在処理中のイベントID |
| `SHIORI3FW.Status` | 初期起動ステータス（`Run` で初期化済） |
| `SHIORI3FW.UniqueID` | Auth.SSTP で使用するID |
| `SHIORI3FW.Capability` | ベースウェアのヘッダ処理能力（汎用配列） |
| `basewarename` | 現在のリクエスト送信元ベースウェア名 |
| `basewarenameex` | 初回起動時のベースウェア名（変わらない） |
| `ghostuptimes` | ゴースト累計起動回数 |

---

## 2. ユーザー設定変数

ゴーストの動作をカスタマイズするために読み書き可能な変数です。

| 変数名 | 初期値 | 内容 |
|--------|--------|------|
| `aitalkinterval` | `180`（秒） | AIトーク（ランダムトーク）の発動間隔。`0` で無効化 |
| `communicateratio` | `0`（%） | AIトーク発動時にコミュニケート開始を選ぶ割合 |

**例：AIトーク間隔を5分に変更する**

```
aitalkinterval = 300
```

---

## 3. SAORI 関連関数

### `FUNCTIONLOAD(dll名)`

SAORI DLLを読み込みます。

- **引数**：`_argv[0]` — SAORIのDLLファイル名
- **戻り値**：成功時 `1`、失敗時 `0`

```
FUNCTIONLOAD('kawari8.dll')
```

---

### `FUNCTIONEX(dll名, 引数0, 引数1, ...)`

SAORIを実行します。

- **引数**：`_argv[0]` — DLLファイル名、`_argv[1]`以降 — SAORIへのArgument0, 1, ...
- **戻り値**：SAORIのResultヘッダの値。実行後、`valueex` 配列および `valueex0`, `valueex1`, ... 変数にValue0, Value1, ... の値が格納されます

```
_result = FUNCTIONEX('kawari8.dll', 'こんにちは')
// 追加の戻り値は valueex[0], valueex[1], ... で参照
```

---

### `SAORI(dll名, 引数0, 引数1, ...)`

`FUNCTIONEX` のシノニム（別名）です。書き方が `FUNCTIONEX` と同一です。

```
_result = SAORI('kawari8.dll', 'こんにちは')
```

---

## 4. ベースウェアプロパティ関数

SSPなど、DIRECTSSTP対応ベースウェアのプロパティを読み書きします。

### `GET_PROPERTY(プロパティ名)`

指定したベースウェアのプロパティ値を取得します。

- **引数**：`_argv[0]` — プロパティ名
- **戻り値**：成功時はプロパティ値の文字列、失敗時は空文字列

```
_vol = GET_PROPERTY('volume')
```

---

### `SET_PROPERTY(プロパティ名, 値)`

指定したベースウェアのプロパティに値を設定します。

- **引数**：`_argv[0]` — プロパティ名、`_argv[1]` — 設定する値
- **戻り値**：成功時 `1`、失敗時 `0`

```
SET_PROPERTY('volume', '80')
```

---

## 5. チェイン制御関数・構文

「チェイントーク」とは、ランダムトーク中に連続する別のトークへ処理を引き継ぐ仕組みです。

---

### チェイン構文（CHAIN マクロ）

トーク関数内で `{{CHAIN` ～ `}}CHAIN` ブロックを使うことで、関数が呼ばれるたびに順番にセリフを進められます。

```
MyChainTalk
{
	{{CHAIN
		'\0\s[0]最初のセリフ。\e'
		'\0\s[0]次のセリフ。\e'
		'\0\s[0]最後のセリフ。\e'
	}}CHAIN
}
```

---

### チェイン区切り文字（`:chain=`）

トークスクリプトの末尾に `:chain=イベント名` を付けることで、次のランダムトーク機会に指定したイベントが呼ばれます。

```
OnMyEvent
{
	'\0\s[0]続きがあるよ。\e' + ':chain=' + 'OnMyEventPart2'
}

OnMyEventPart2
{
	'\0\s[0]これが続きです。\e'
}
```

---

## 6. 遅延イベント関数

指定した秒数後に、特定のイベントを1度だけ発動させます。発動はOnSecondChange（1秒ごと）のタイミングで処理されます。

### `SHIORI3FW.SetDelayEvent(イベント名, 秒数[, Reference0, ...])`

遅延イベントを登録します。

- **引数**：
  - `_argv[0]` — 発動させるイベント名。空文字列でキャンセル
  - `_argv[1]` — 遅延秒数
  - `_argv[2]`以降 — イベント発動時にセットする Reference 値（省略可）
- **戻り値**：なし

```
// 10秒後にOnMyDelayedEventを発動
SHIORI3FW.SetDelayEvent('OnMyDelayedEvent', 10)

// キャンセル
SHIORI3FW.SetDelayEvent('', 0)
```

---

### `SHIORI3FW.GetDelayEvent()`

現在登録されている遅延イベントの情報を取得します。

- **戻り値**：`(イベント名, 残り秒数, Referenceの配列)` のタプル

---

## 7. レスポンスヘッダ追加関数

イベントハンドラのレスポンスに任意のヘッダを追加します。設定はリクエストごとにリセットされます。

### `SHIORI3FW.PushAdditionalReturn(ヘッダ名, 値)`

任意のレスポンスヘッダを追加します。

- **引数**：`_argv[0]` — ヘッダ名、`_argv[1]` — 値
- **戻り値**：なし

```
SHIORI3FW.PushAdditionalReturn('X-My-Header', 'MyValue')
```

---

### `SHIORI3FW.Push_X_SSTP_PassThru(名前, 値)`

`X-SSTP-PassThru-名前` ヘッダを追加します。SSTPパススルーに使用します。

- **引数**：`_argv[0]` — パススルー名（ヘッダ名のサフィックス）、`_argv[1]` — 値
- **戻り値**：なし

```
SHIORI3FW.Push_X_SSTP_PassThru('MyData', 'hello')
// → "X-SSTP-PassThru-MyData: hello" がヘッダに追加される
```

---

### レスポンス用特殊変数

イベントハンドラ内でこれらの変数に値を設定すると、レスポンスヘッダに自動的に付加されます。

| 変数名 | ヘッダ | 内容 |
|--------|--------|------|
| `res_reference` または `res_reference0` ～ `res_reference31` | `Reference0:` ～ | イベント応答のReference値。配列または個別変数で指定 |
| `marker` または `res_marker` | `Marker:` | SSTPマーカー値 |
| `res_securitylevel` | `SecurityLevel:` | セキュリティレベル |
| `res_valuenotify` | `ValueNotify:` | ValueNotify値 |

```
OnMyEvent
{
	res_reference0 = 'value0'
	res_reference1 = 'value1'
	'\0\s[0]処理しました。\e'
}
```

---

## 8. テキスト処理関数

### `SHIORI3FW.RemoveAllTags(テキスト)`

さくらスクリプトタグ（`\0`, `\s[0]` など）をすべて除去した文字列を返します。

- **引数**：`_argv[0]` — 処理対象のテキスト
- **戻り値**：タグを除去した文字列

```
_plain = SHIORI3FW.RemoveAllTags('\0\s[0]こんにちは。\e')
// → "こんにちは。"
```

---

## 9. 話者状態確認関数

### `SHIORI3FW.CanTalk()`

現在ゴーストが喋れる状態かどうかを返します。

`status` ヘッダに `talking`・`choosing`・`minimizing`・`timecritical` のいずれかが含まれている場合は喋れないと判断します。

- **戻り値**：喋れる場合 `1`、喋れない場合 `0`

```
if SHIORI3FW.CanTalk {
	// ランダムトーク発動
}
```

---

## 10. インストール済みゴースト関連関数・変数

### 自動構築される変数

`OnNotifyInstalledGhostName` などのイベントを受け取ると、以下の変数が自動的に更新されます。

| 変数名 | 内容 |
|--------|------|
| `installedghostlist` | インストール済みゴーストのゴースト名配列 |
| `installedsakuralist` | インストール済みゴーストのさくら名配列 |
| `installedkerolist` | インストール済みゴーストのケロ名配列 |

インデックスは対応しており、`installedghostlist[i]` のゴーストのさくら名は `installedsakuralist[i]` です。

---

## 11. 変数管理ユーティリティ

### `SHIORI3FW.RegisterTempVar(変数名, ...)`

終了時（unload時）に自動削除するグローバル変数を登録します。保存が不要な一時的なグローバル変数の管理に使います。

- **引数**：削除対象の変数名（複数指定可）
- **戻り値**：なし

```
myTempVar = '一時データ'
SHIORI3FW.RegisterTempVar('myTempVar')
```

---

### `SHIORI3FW.SaoriUnloadAll()`

現在ロードされているすべてのSAORI DLLをアンロードします。

- **引数**：なし
- **戻り値**：なし

---

## 12. 配列ユーティリティ関数

### `JOIN(値0, 値1, ..., 区切り文字)`

複数の値を指定の区切り文字でつなげた文字列を返します。

- **引数**：最後の引数が区切り文字、それ以外が連結する値
- **戻り値**：連結した文字列

```
_s = JOIN('あ', 'い', 'う', ',')
// → "あ,い,う"
```

---

### `REVERSE(値0, 値1, ...)`

引数の順序を逆にした配列を返します。

- **戻り値**：逆順の配列

```
_a = REVERSE('a', 'b', 'c')
// → ('c', 'b', 'a')
```

---

### `UNIQUE(値0, 値1, ...)`

引数から重複を除いた配列を返します（`ARRAYDEDUP` のラッパー）。

- **戻り値**：重複除去後の配列

```
_a = UNIQUE('a', 'b', 'a', 'c')
// → ('a', 'b', 'c')
```

---

### `SPLITEX(文字列, 区切り文字)`

文字列を区切り文字で分割し、空要素を削除した配列を返します（`SPLIT` の空要素除去版）。

- **引数**：`SPLIT` と同じ
- **戻り値**：空要素を除いた配列

```
_a = SPLITEX('a,,b,,c', ',')
// → ('a', 'b', 'c')
```

---

### `MAX(値0, 値1, ...)`

引数の中の最大値を返します。

```
_m = MAX(3, 1, 4, 1, 5)
// → 5
```

---

### `MIN(値0, 値1, ...)`

引数の中の最小値を返します。

```
_m = MIN(3, 1, 4, 1, 5)
// → 1
```

---

### `AVERAGE(値0, 値1, ...)`

引数の平均値を返します。

```
_avg = AVERAGE(10, 20, 30)
// → 20
```

---

## 13. 疑似イベント

これらの関数をゴーストの辞書に定義することで、特定のタイミングで呼び出されます。

### `OnSHIORI3FW.SurfaceRestore`

AIトーク間隔の2/3が経過したときに発火する疑似イベントです。
トーク前にサーフィスをデフォルトに戻す処理などに使います。

```
OnSHIORI3FW.SurfaceRestore
{
	'\0\s[0]\1\s[10]\e'
}
```

---

### `OnSHIORI3FW.ChangeSelfInfo`

以下の情報が更新されたときに呼び出されます：
- ゴースト名（`SHIORI3FW.GhostName`）
- シェル名・パス（`SHIORI3FW.ShellName` / `SHIORI3FW.ShellPath`）
- バルーン名・パス（`SHIORI3FW.BalloonName` / `SHIORI3FW.BalloonPath`）

```
OnSHIORI3FW.ChangeSelfInfo
{
	// シェルが変わったときの処理など
}
```

---

### `OnSHIORI3FW.WindowCreate`

キャラウィンドウが新たに生成されたときに呼び出されます。
`reference[0]` にスコープ番号が入ります。

---

### `OnSHIORI3FW.WindowDestroy`

キャラウィンドウが破棄されたときに呼び出されます。
`reference[0]` にスコープ番号が入ります。

---

## 付録：config.dic の主要設定

`yaya_base/config.dic` でシステム動作を変更できます。

| 定数名 | 初期値 | 内容 |
|--------|--------|------|
| `TALK_INTERVAL` | `180` | `aitalkinterval` の初期値（秒） |
| `COM_RATIO` | `0` | `communicateratio` の初期値（%） |
| `SHIORI3FW.IGLIST_ACCEL` | `1` | `1` でSSP/CROWのNOTIFYイベントを使ってゴーストリストを高速構築 |
| `SHIORI3FW.IGLIST_MAX` | `0` | ファイル走査時のゴースト最大取得数。`-1` で無制限、`0` で取得しない |
| `SHIORI3FW.RES_REF_MAX` | `32` | `res_reference*` 変数の上限インデックス |
| `SHIORI3FW.AUTO_DATA_CONVERT` | `0` | `1` でSAORI戻り値などを自動タイプ変換（AYA5互換） |
| `SHIORI3FW.REF_ACCEL` | `0` | `1` で `reference0` 等の変数を作成せず `reference[0]` のみ使用（高速化） |
| `SHIORI3FW.ENABLE_DELAYED_EVAL` | `0` | `:eval=` 遅延EVAL機能の有効化（**セキュリティ上の理由から無効推奨**） |

---

# optional.dic ユーティリティ関数リファレンス

`yaya_base/optional.dic` が提供する補助的なユーティリティ関数群です。
shiori3.dic とともに自動的に読み込まれます。

---

## 15. タグエスケープ関数

さくらスクリプトのタグを文字列として表示したいときや、外部入力をスクリプトに埋め込む前の無害化に使います。

### `SHIORI3FW.EscapeDangerousTags(テキスト)`

危険な操作を行うタグ（ブラウザ起動・メーラー起動・パッシブモード切り替え等）のバックスラッシュだけを `\\` にエスケープします。安全なタグはそのまま残ります。

- **引数**：`_argv[0]` — 処理対象のテキスト
- **戻り値**：危険タグのみエスケープされた文字列

```
_safe = SHIORI3FW.EscapeDangerousTags(外部から受け取ったテキスト)
```

---

### `SHIORI3FW.EscapeAllTags(テキスト)`

すべてのさくらスクリプトタグ（`\` で始まるタグ全般）をエスケープします。
ユーザー入力をそのままバルーンに表示したいときに使います。

- **引数**：`_argv[0]` — 処理対象のテキスト
- **戻り値**：全タグがエスケープされた文字列

```
_display = SHIORI3FW.EscapeAllTags('\0\s[0]これはタグではなくテキストです。\e')
// → "\\0\\s[0]これはタグではなくテキストです。\\e" として表示される
```

> `RemoveAllTags`（shiori3.dic）はタグを**削除**しますが、こちらはタグを**エスケープして表示**します。

---

### `SHIORI3FW.TranslateSystemChar(テキスト[, 置換文字])`

YAYA の演算子・区切り文字などシステム予約文字を指定の文字に置き換えます。
変数名や識別子として利用できる安全な文字列を生成したいときに便利です。

- **引数**：`_argv[0]` — テキスト、`_argv[1]` — 置換後の文字（省略時は `_`）
- **戻り値**：予約文字を置換した文字列

```
_varname = SHIORI3FW.TranslateSystemChar('hello world!')
// → "hello_world_"

_varname = SHIORI3FW.TranslateSystemChar('foo/bar', '-')
// → "foo-bar"
```

---

## 16. 文字列整形関数

バルーン内メニューなど、表示幅を揃えたいときに使う関数群です。
文字数はすべて**半角換算**（全角1文字＝半角2文字）で指定します。

### `SHIORI3FW.MakeLongText(テキスト, 文字数)`

テキストが指定の半角換算文字数に満たない場合、末尾にスペースを補完して長さを揃えます。
すでに指定長以上なら元のテキストをそのまま返します。

- **引数**：`_argv[0]` — テキスト、`_argv[1]` — 目標の半角換算文字数
- **戻り値**：末尾スペースで埋めた文字列

```
_padded = SHIORI3FW.MakeLongText('短いテキスト', 20)
```

---

### `SHIORI3FW.MakeShortText(テキスト, 文字数)`

テキストが指定の半角換算文字数を超える場合、末尾を切り詰めて `...` または `..` を付加します。
指定長以内なら元のテキストをそのまま返します。

- **引数**：`_argv[0]` — テキスト、`_argv[1]` — 最大の半角換算文字数
- **戻り値**：切り詰められた文字列

```
_short = SHIORI3FW.MakeShortText('これはとても長いテキストです', 10)
// → "これは..." のように切り詰められる
```

---

### `SHIORI3FW.MakeJustText(テキスト, 文字数)`

`MakeLongText` と `MakeShortText` を組み合わせて、テキストをちょうど指定の半角換算文字数に整形します。

- **引数**：`_argv[0]` — テキスト、`_argv[1]` — 目標の半角換算文字数
- **戻り値**：指定長に揃えた文字列

```
// メニュー項目の幅を20半角文字で揃える例
_item = SHIORI3FW.MakeJustText('設定', 20)
```

---

## 17. バルーン初期化関数

### `SHIORI3FW.InitBalloons()`

現在有効なすべてのスコープのバルーンをクリアするさくらスクリプトを生成します。
マルチスコープ環境でバルーンをまとめてリセットしたいときに使います。

- **引数**：なし
- **戻り値**：全スコープの `\p[n]\c\b[-1]` を連結したさくらスクリプト文字列

```
OnMyEvent
{
	SHIORI3FW.InitBalloons + '\0\s[0]初期化しました。\e'
}
```

---

## 18. FMO 関連関数・変数

FMO（File Mapping Object）とは、SSPが管理する「現在起動中のゴースト情報」の共有メモリです。
これらの関数を使うと、他のゴーストの存在確認や情報取得が可能です。

### `SHIORI3FW.IsGhostExist(さくら名[, FMO名称])`

指定したさくら名のゴーストが現在起動しているかどうかを調べます。

- **引数**：`_argv[0]` — 調べるゴーストのさくら名、`_argv[1]` — FMO名称（省略時は `'Sakura'`）
- **戻り値**：存在すれば `1`、存在しなければ `0`

```
if SHIORI3FW.IsGhostExist('ゆっくり霊夢') {
	'\0\s[0]霊夢がいるよ！\e'
}
```

> 内部で `SHIORI3FW.RefreshFMOTable` を呼び出してFMO情報を最新化します。

---

### `SHIORI3FW.RefreshFMOTable([FMO名称[, 無視するHWND]])`

FMO を読み取り、起動中のゴースト情報をグローバル変数に格納します。
FMO の内容が前回から変わっていない場合は再解析をスキップします（キャッシュ機能）。

- **引数**：
  - `_argv[0]` — FMO名称。省略時は `'Sakura'`。Unicode版は `'SakuraUnicode'`
  - `_argv[1]` — 一覧から除外するゴーストのHWND。省略時は自分自身（`SHIORI3FW.HWnd[0]`）が自動的に除外される
- **戻り値**：なし（グローバル変数を更新）

通常は `IsGhostExist` 経由で自動的に呼び出されますが、直接呼ぶことも可能です。

---

### FMO 関連グローバル変数

`RefreshFMOTable` 実行後に参照できます。

| 変数名 | 内容 |
|--------|------|
| `SHIORI3FW.SakuraNameList` | 起動中ゴーストのさくら名の配列 |
| `SHIORI3FW.FMOTable` | FMOテーブルの文字列。各エントリは `id\|name\|keroname\|hwnd\|kerohwnd\|path\|ghostpath` の形式でカンマ区切り |
| `SHIORI3FW.FMOCache` | 前回読み取ったFMOの生データ（キャッシュ用。直接参照するケースは少ない） |

**`SHIORI3FW.FMOTable` のエントリ構造（`|` 区切り）：**

| インデックス | 内容 |
|-------------|------|
| `[0, '\|']` | FMO エントリID |
| `[1, '\|']` | さくら名（name） |
| `[2, '\|']` | ケロ名（keroname） |
| `[3, '\|']` | さくら側HWND |
| `[4, '\|']` | ケロ側HWND |
| `[5, '\|']` | シェルパス（path） |
| `[6, '\|']` | ゴーストパス（ghostpath） |

```
// 起動中の全ゴースト名を列挙する例
SHIORI3FW.RefreshFMOTable
_list = SHIORI3FW.SakuraNameList
_n = ARRAYSIZE(_list)
for _i = 0; _i < _n; _i++ {
	// _list[_i] にさくら名が入っている
}
```

> **注意**：「一時起動」（SSTPVIEWERやダミーエントリ等）のゴーストはFMOテーブルから自動的に除外されます。

---

## 19. その他ユーティリティ

### `SHIORI3FW.HTTPCodeToMessage(コード)`

ネットワーク更新などで返ってきたHTTPステータスコードを、日本語のエラーメッセージに変換します。

- **引数**：`_argv[0]` — HTTPステータスコード文字列または特殊文字列
- **戻り値**：対応する日本語メッセージ文字列。対応するコードがない場合は空文字列

| コード | 戻り値 |
|--------|--------|
| `'403'` | `'アクセス拒否'` |
| `'404'`, `'410'` | `'ファイル無し'` |
| `'500'`, `'502'`, `'503'` | `'サーバ側の不調'` |
| `'timeout'` | `'タイムアウト'` |
| `'fileio'` | `'ファイル書き込みエラー'` |
| `'artificial'` | `'手動中断'` |

```
OnNetworkUpdateComplete
{
	if reference[1] != '200' {
		_msg = SHIORI3FW.HTTPCodeToMessage(reference[1])
		'\0\s[0]更新に失敗しました：%(_msg)\e'
	}
}
```
