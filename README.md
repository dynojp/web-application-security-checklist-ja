# 安全なウェブサイトの作り方 チェックリスト

IPA（独立行政法人情報処理推進機構）による「安全なウェブサイトの作り方」（改訂第 7 版）のチェックリストです。

著作権は IPA に帰属します。
オリジナルからの間違い等があればすべて [gh640](/gh640) のミスによるものです。

## 脆弱性の種類

1. SQL インジェクション
2. OS コマンド・インジェクション
3. パス名パラメータの未チェック／ディレクトリ・トラバーサル
4. セッション管理の不備
5. クロスサイト・スクリプティング
6. CSRF （クロスサイト・リクエスト・フォージェリ）
8. HTTP ヘッダ・インジェクション
9. メールヘッダ・インジェクション
10. クリックジャッキング
11. バッファオーバーフロー
12. アクセス制御や認可制御の欠落

## 脆弱性の種類別チェック項目

### 1. SQL インジェクション

- [ ] SQL 文の組み立ては全てプレースホルダで実装する。
- [ ] SQL 文の構成を文字列連結により行う場合は、アプリケーションの変数を SQL 文のリテラルとして正しく構成する。
- [ ] ウェブアプリケーションに渡されるパラメータに SQL 文を直接指定しない。
- [ ] エラーメッセージをそのままブラウザに表示しない。
- [ ] データベースアカウントに適切な権限を与える。

### 2. OS コマンド・インジェクション

- [ ] シェルを起動できる言語機能の利用を避ける。
- [ ] シェルを起動できる言語機能を利用する場合は、その引数を構成する全ての変数に対してチェックを行い、あらかじめ許可した処理のみを実行する。

### 3. パス名パラメータの未チェック／ディレクトリ・トラバーサル

- [ ] 外部からのパラメータでウェブサーバ内のファイル名を直接指定する実装を避ける。
- [ ] ファイルを開く際は、固定のディレクトリを指定し、かつファイル名にディレクトリ名が含まれないようにする。
- [ ] ウェブサーバ内のファイルへのアクセス権限の設定を正しく管理する。
- [ ] ファイル名のチェックを行う。

### 4. セッション管理の不備

- [ ] セッション ID を推測が困難なものにする。
- [ ] セッション ID を URL パラメータに格納しない。
- [ ] HTTPS 通信で利用する Cookie には `secure` 属性を加える。
- [ ] ログイン成功後に、新しくセッションを開始する。
- [ ] ログイン成功後に、既存のセッション ID とは別に秘密情報を発行し、ページの遷移ごとにその値を確認する。
- [ ] セッション ID を固定値にしない。
- [ ] セッション ID を Cookie にセットする場合、有効期限の設定に注意する。

### 5. クロスサイト・スクリプティング

HTMLテキストの入力を許可しない場合の対策:

- [ ] ウェブページに出力する全ての要素に対して、エスケープ処理を施す。
- [ ] URLを出力するときは、「 `http://` 」や 「 `https://` 」で始まる URL のみを許可する。
- [ ] `<script>` ... `</script>` 要素の内容を動的に生成しない。
- [ ] スタイルシートを任意のサイトから取り込めるようにしない。
- [ ] 入力値の内容チェックを行う。

HTMLテキストの入力を許可する場合の対策:

- [ ] 入力された HTML テキストから構文解析木を作成し、スクリプトを含まない必要な要素のみを抽出する。
- [ ] 入力された HTML テキストから、スクリプトに該当する文字列を排除する。

全てのウェブアプリケーションに共通の対策:

- [ ] HTTP レスポンスヘッダの `Content-Type` フィールドに文字コード（ `charset` ）の指定を行う。
- [ ] Cookie 情報の漏えい対策として、発行する Cookie に `HttpOnly` 属性を加え、 TRACE メソッドを無効化する。
- [ ] クロスサイト・スクリプティングの潜在的な脆弱性対策として有効なブラウザの機能を有効にするレスポンスヘッダを返す。


### 6. CSRF （クロスサイト・リクエスト・フォージェリ）

- [ ] 処理を実行するページを POST メソッドでアクセスするようにし、その「 `hidden` パラメータ」に秘密情報が挿入されるよう、前のページを自動生成して、実行ページではその値が正しい場合のみ処理を実行する。
- [ ] 処理を実行する直前のページで再度パスワードの入力を求め、実行ページでは、再度入力されたパスワードが正しい場合のみ処理を実行する。
- [ ] `Referer` が正しいリンク元かを確認し、正しい場合のみ処理を実行する。
- [ ] 重要な操作を行った際に、その旨を登録済みのメールアドレスに自動送信する。

### 8. HTTP ヘッダ・インジェクション

- [ ] ヘッダの出力を直接行わず、ウェブアプリケーションの実行環境や言語に用意されているヘッダ出力用 API を使用する。
- [ ] 改行コードを適切に処理するヘッダ出力用 API を利用できない場合は、改行を許可しないよう、開発者自身で適切な処理を実装する。
- [ ] 外部からの入力の全てについて、改行コードを削除する。

### 9. メールヘッダ・インジェクション

- [ ] メールヘッダを固定値にして、外部からの入力はすべてメール本文に出力する。
- [ ] ウェブアプリケーションの実行環境や言語に用意されているメール送信用APIを使用する（ 8-(i) を採用できない場合）。
- [ ] HTML で宛先を指定しない。
- [ ] 外部からの入力の全てについて、改行コードを削除する。

### 10. クリックジャッキング

- [ ] HTTP レスポンスヘッダに、 `X-Frame-Options` ヘッダフィールドを出力し、他ドメインのサイトからの `frame` 要素や `iframe` 要素による読み込みを制限する。
- [ ] 処理を実行する直前のページで再度パスワードの入力を求め、実行ページでは、再度入力されたパスワードが正しい場合のみ処理を実行する。
- [ ] 重要な処理は、一連の操作をマウスのみで実行できないようにする。

### 11. バッファオーバーフロー

- [ ] 直接メモリにアクセスできない言語で記述する。
- [ ] 直接メモリにアクセスできる言語で記述する部分を最小限にする。
- [ ] 脆弱性が修正されたバージョンのライブラリを使用する。

### 12. アクセス制御や認可制御の欠落

- [ ] アクセス制御機能による防御措置が必要とされるウェブサイトには、パスワード等の秘密情報の入力を必要とする認証機能を設ける。
- [ ] 認証機能に加えて認可制御の処理を実装し、ログイン中の利用者が他人になりすましてアクセスできないようにする。

## 出典

- [安全なウェブサイトの作り方：IPA 独立行政法人 情報処理推進機構](https://www.ipa.go.jp/security/vuln/websecurity.html)
