# 現場で使えるRuby on Rails

## 1. Ruby

### irb でオブジェクトを調べる

```
# class
> "氏名".class
=> String

# object_id(オブジェクト固有に与えられたid)
> "氏名".class
=> 123412341234
```

### 変数の種類

* ローカル変数: 処理が終われば捨てられる
* インスタンス変数: オブジェクト内部に保持される

### irb でRubyファイルを実行

```
> require './file_name.rb'
```

### return の使い分け

* メソッドの途中でメソッドを抜ける: return を使う
* メソッドの最後で返り値を指定する: return を使わない

### セッターとゲッター

* セッター: インスタンス変数を代入する為のメソッド
* ゲッター: インスタンス変数を参照するメソッド

```
class User
  # セッター
  def name = (name)
    @name = name
  end

  # ゲッター
  def name
    @name # ゲッター
  end
end
```

### 属性(Attribute)

* オブジェクトの抱えるデータの一種
* 主にデータの識別で使用する

### attr_acccessor

クラスに属性を追加する

```
# User にname 属性のゲッター, セッターを追加
class User
  attr_accessor :name
end

# User にname 属性のゲッターを追加
class User
  attr_reader :name
end
# User にname 属性のセッターを追加
class User
  attr_writer :name
end
```

属性を追加してメソッドで呼び出し

```
class User
  attr_accessor :name, :address, :email

  def profile
    "#{name}(#{address})
  end
end
```

```
> user = User.new
> user.name = "name1"
> user.address = "fukuoka"
> user.profile
=> "name1(fukuoka)"
```

### initialize

インスタンス作成時に値をセットする

```
# name, address, email をインスタンス生成時に作成し、値を変更できないようにする
class User
  # attr_reader にすることで読み込み専用
  attr_reader :name, :address, :email
  def initialize(name, address, email)
    @name = name
    @address = address
    @email = email
  end
end
```

### private

* メソッドの呼び出しに制限を掛ける
* 不用意に呼び出されたくないメソッドに対して使用

```
class Person
  def initialize(money)
    @money = money
  end

  def billionaire?
    money >= 100000000
  end

  private
    def money
      @money
    end
end
```

```
> Person.new(5000000)

# public メソッド
> person.billionaire?
=> false

# private メソッド
> person.money
=> NoMethodError...
```

### オーバーライド

親クラスのメソッドを子クラスで上書きする

```
class BaseTask
  def puts_message
    puts 'BaseTaskのタイトル'
  end

# BaseTask を継承
class Task < Basetask
  def puts_massage
    # Basetask のputs_message 呼び出し
    super
    # Task のputs_message 呼び出し
    puts 'Task のタイトル'
  end
end
```

```
> task = Task.new
> puts task.puts_message
BeseTask のタイトル
Task のタイトル
```

### モジュール(Mix-in)

一連の振る舞いをまとめてメソッドに読み込む

```
module Chatting
  def chat
    "hello"
  end
end

module Weeping
  def weep
    "しくしく"
  end
end

# Chatting, Weeping をMix-in
class Human
  include Chatting
  include Weeping
end
```

```
> taro = Human.new
> taro.chat
=> "hello"
> taro.weep
=> "しくしく"
```

### 例外

エラーが発生するかもしれないコードを補足して処理をする

```
# 例外が発生するかもしれないコード
begin
  do_something
# 例外のメッセージをeに受け取り,classとmessage を出力
rescue SomeSpecialError => e
  puts "#{e.class}(#{e.message})が発生しました。処理を事項します。"
end
```

### nil ガード

代入する際にnil にならないようにデフォルト値を設定する

```
# @chilidren がnil の場合は空の配列を代入する
def chilidren
  @children ||= []
end
```

### ぼっち演算子(&.)

レシーバがnil でもエラーが発生しなくなる

```
class User
  def name
    "名前"
  end
end
```

```
> user = User.new
> user.name
=> "名前"

# レシーバオブジェクトがnil だとエラーが出る
> object = nil
> object.name
=> NoMethodError...

# ぼっち演算子だとnil でもエラーが出ないｊ
> object&.name
=> nil
```

object が空の場合はname にnil を代入する

```
name = object&.name
```

### %記法

配列を表す

#### %w

全ての要素が文字列

```
> ary = %w(apple banana orange)
> p ary
=> ["apple", "banana", "orange"]
```

#### %i

全ての要素がシンボル

```
ary = %i(apple, banana, orange)
> p ary
=> [:apple, :banana, :orange]
```

### map

配列の各要素に対してブロックを実行した結果を新たな配列にして返す

```
class User
  attr_accessor :name
end

user1 = User.new
user1.name = "fukuoka"
user2 = User.new
user2.name = "kagoshima"
user3 = User.new
user3.name = "miyazaki"

users = [user1, user2, user3]

# パターン1
names = users.map do |user|
  user.name
end
=> ["fukuoka", "miyazaki", "kagoshima"]

# パターン2
names = users.map{ |user| user.name }
=> ["fukuoka", "miyazaki", "kagoshima"]

# パターン3
names = users.map(&:name)
=> ["fukuoka", "miyazaki", "kagoshima"]
```

## 2. 環境

### bundle exec

* Bundler が管理するGem 環境でコマンドを実行する
* 基本的にはこのコマンドを使ったほうが良い？

### bin/rails

* ./bin/rails を実行するという意味
* `bundle exec rails` と実行内容は同じ

### postgreSQL サーバ起動

```
# 起動
$ brew e
services start postgresql
# 停止
$ brew services stop postgresql
```

### postgreSQL を使ってのアプリ作成

postgreSQL をDBに使う場合はアプリ作成後にサーバを起動して開発を進める

```
$ rails new app_name -d postgresql
$ brew services start postgresql
$ bin/rails db:create
$ bin/rails s
```

DB 確認

```
$ psql -l
```

ブラウザで確認

http://localhost:3000/

### rails generate

```
# User モデル作成(単数形, キャメルケース)
$ bin/rails g model User
# users コントローラ作成(複数形, スネークケース)
$ bin/rails g controller users
```

### YAML

* database.yml で使用されている
* データを表現する為のフォーマット
* インデントで階層構造を表現
* `キー: 値` で表現
* エイリアスを使い共通化できる

```
animal: &animal
  cat: 'ネコ'
  dog: '犬'

animal_1:
  <<: *animal
  caw: '牛'

# 下記と同様
animal_1:
  cat: 'ネコ'
  dog: '犬'
  caw: '牛'
```

## 3. 基礎

### DB の種類

1. development: 開発
2. test: テスト
3. production: ユーザーが利用可能な状態で稼働させる

### Slim

* HTML テンプレートエンジン
* インデントで階層表現
* 開始, 終了タグの省略

2つのGem を導入

```
gem 'slim-rails'
gem 'html2slim'
```

app/views/layouts/ をSlim に変換

```
$ bundle exec erb2slim app/views/layouts/ --delete
```

### Bootstrap

bootstrap gem をインストールする

```
gem 'bootstrap'
```

scss ファイル設置

```
# application.css 削除
$ rm app/assets/stylesheets/application.css
# application.scss 作成
$ touch app/assets/stylesheets/application.scss
```

application.scss に追記

```
# bootstrap 読み込み
@import "bootstrap";
```

### ローカライズ

英語以外の言語で表示する

```
# 日本語翻訳ファイルをダウンロードする
$ wget https://raw.githubusercontent.com/svenfuchs/rails-i18n/master/rails/locale/ja.yml --output document=config/locales/ja.yml
```

config/initializers/locale.rb 作成 > 追記

```
Rails.application.config.i18n.default_locale = :ja
```

### resources

index, show, new, create, edit, update, destroy を一括追加

### Flash

redirect_to でのリダイレクト先でメッセージを表示する

```
redirect_to tasks_url, notice: "タスク「#{task.name}」を登録しました"
# 下記と同じ
flash[:notice] = "タスク「#{task.name}」を登録しました"
redirect_to tasks_url
```

application.html.slim に追記

```
.container
  - if flash.notice.present?
    .alert.alert-success= flash.notice
  = yield
```

### simple_format

テキストに含まれる危険なHTML を除外し改行などを適切な形で表示する

```
# h メソッドをon にしてdiv タグで表示内容を囲む
simple_format(h(@task.description), {}, sanitize: false, wrapper_tag: "div")
```

### パーシャル

* view ファイルの共通化
* ファイル名は頭にアンダースコア
* ディレクトリはapp/views/controller_name/_file_name.html.slim

```
# パーシャルの読み込み
= render partial: 'form', locales: { task: @task }
```

`{ task: @task }` とする事でインスタンス変数の定義に依存しないので再利用性が高まる

## 4. 発展

### 環境別にマイグレーション

```
# 本番環境
$ bin/rails db:migrate RAILS_ENV=production
# テスト環境
$ bin/rails db:migrate RAILS_ENV=test
```

### バージョンの上げ下げ

```
# up
$ rails db:migrate
# down
$ rails db:rollback
# redo
$ rails migrate:redo
```

`redo` はバージョンを一つ戻しもう一度上げる。動作確認の為に使用する

### schema.rb

* migrate すると自動的に出力される
* DB についての詳細が書かれているのでドキュメントとして使える

### NOT NULL

値がnull の場合はDB に保存しないようにマイグレーションファイルに追記すること

```
def change
  create_table :task do |t|
    t.string :name, null: false
```

DB 作成後に追加する場合

```
# change_column_null でNOT NULL を付与する
def change
  change_column_null :tasks, :name, false
end
```

### ユニークインデックス

DB 保存時に重複したデータを弾く

```
def change
  t.timestamps
  t.index :name, unique: true
```

DB 作成後に変更

```
def change
  add_index :tasks, name, unique: true
end
```

### validate

DB 保存前のデータの検証

#### コンソールでvalidate をスキップ

```
> task.save(validate: false)
```

#### コンソールでの確認

```
# エラー表示
> task.errors
# 検証エラーメッセージ
> task.errors.full_messages
# DB に保存されたか
> task.persisted?
```

### コールバック

* DB 保存時や削除時に任意の処理を挟むこと
* model に追記する
* model でprivateメソッドを作成し、コールバックメソッドで呼び出す

### トランザクション

コールバック全体を1つの塊で実行する事でデータの整合性を保つ機能

### セッション

ブラウザからのリクエストを保持する機能

```
# 値を保持
session[:user_id] = @user.id
# 値を取り出す
@user_id = session[:user_id]
```

### cookie

* 複数のリクエスト間で共有したい状態をブラウザ側い保持する仕組み
* Rails ではログイン状態を保持するなどに使われる

### password_digest

* User モデル作成時のRails標準のパスワード用の属性名
* データが漏洩してもパスワードは復元できないので安全
* has_secure_password を使う為に必要

```
# password_digest を使用してUser モデルを作成
$ bin/rails g model user name:string email:string password_digest:string 
```

### has_secure_password

* パスワードをハッシュ化してセキュリティを向上させる
* bcrypt gem が必要

#### 1. bcrypt gem 追加

```
# Gemfile のコメントアウトを外す
gem 'bcrypt', '-> 3.1.7'
```

#### 2. has_secure_password 読み込み

モデルに追記

```
class User < ApplicationRecord
  has_secure_password
end
```

データベースのカラムに2つの属性が追加される

* password: 生のパスワードを格納する属性
* password_confimation: 確認用パスワードを一時的に格納する属性

### authenticate メソッド

* has_secure_password で自動で追加される認証用メソッド
* 引数で受け取ったパスワードをUserオブジェクトに保存されているdigest と一致しているか調べ認証の可否を決める

ログイン機能を実行するsession_controller.rb で使用する

```
def create
  # メールアドレスでユーザ検索
  user = User.find_by(email: session_params[:email])

  # ユーザが見つかった場合はauthenticate でパスワードを認証
  # メールアドレスが見つからなかったら場合に例外になるずnil にする為にぼっち演算子を使う
  if user&.authenticate(session_params[:password])
    session[:user_id = user.id]
    redirect_to root_path, notice: 'ログインしました'
  else
    render :new
  end
end

private

  def session_params
    params.require(:session).permit(:email, :password)
  end
```

* ログインしていない: session[:user_id] > nil
* ログイン時: session[:user_id] > ログイン中のユーザID

### Filter

* アクションを処理する前に任意の処理を挟みアクションを制限する
* ログイン機能でアクションに制限を掛けたい場合などに使用する
* 共通化させておいて特定のコントローラには適用させない使い方が一般的

```
# 特定のコントローラに適用させない
# 適用させたくないコントローラに記述
# 例) login_required をSessionコントローラに適用させたくない場合

class SessionsController < ApplicationController
  skip_before_action :login_required
  .
end
```

### Association

* 複数のモデルクラス同士を紐つける事でデータに関連性をもたせる
* ユーザ別の投稿内容などで使用

#### 関連の追加方法

1. 1対多の"多"にを示す外部キーをもたすマイグレーションを実行する
2. マイグレーションを実行する際は"多"のデータを削除してからカラムを追加する
3. "1"のモデルに`has_many` を使い関連させるモデルを定義する
4. "多"のモデルに`belogs_to` を使い追従するもモデルを定義する

#### その他のAssosiation

* 1対1: `has_one`
* 多対多: `has_and_belongs_to_many`

### 絞り込み, 検索

以下の3点で絞り込みを実行する

1. 起点(Model またはhas_many関連)
2. 条件(クエリーメソッド)
3. 実行

```
# 起点. 条件. 実行
User.where(admin:true).first
```

#### to_sql

* クエリーメソッドを呼んだ時点で絞り込みは実行されず、実行にあたるメソッドが呼ばれた時点で実行される
* 上記理由からコンソール等でクエリを確認する際に使用する

```
# to_sql を使い絞り込み結果を確認
> User.where(admin: true).to_sql
=> "SELECT \"users\".* FROM \"users\" WHERE \"users\".\"admin\" = 'TRUE'
```

### scope

* クエリーメソッドをまとめて名前をつける事で再利用しやすくすること
* Model でスコープを定義 > Controller で呼び出して使用

```
# Model にrecentスコープを定義する
class Task < ApplicationRecord
  belongs_to :user
  scope :recent, -> { order(created_at: :desc) }
  .
end
```

```
# コントローラでの呼び出し
def set_task
  @task = current_user.tasks.recent
end
```

### URLを自動リンク化

* rails_autolink Gem を使用
* view で呼び出す事で使用する

```
gem 'rails_autolink'
```

```
# auto_link 呼び出し
tr
  th= Task.human_attribute_name(:description)
  td= auto_link(simple_format(h(@task.description), {}, sanitaize: false, wrapper_tag: "div"))
```

## 5. Test

### RSpec

* BDD(振舞駆動開発)テスティングフレームワーク
* 要求仕様をドキュメントに記述する感覚でテストケースを記述する

### Capybara

* E2E(End-to-End)テスト用フレームワーク
* Rails5.1 からRails に同梱
* ブラウザ操作をシュミレートする事でブラウザ操作をテストできる

### Fixture

* YAML形式でテーブルレコードに対するデータを定義し、DB に直接反映させる仕組み
* 登録に関する複雑な処理が苦手

### FactoryBot

* テストデータ作成サポートGem
* Rails 標準のFixture の代替で使用する
* Fixture に比べ複雑な制御が可能

### テストの種類

* システムテスト: ブラウザを通して挙動を外部的に行う > System Spec, Feature Spec
* 結合テスト: 機能の連続性の確認, 内部的なテスト > Request Spec
* 機能テスト: コントローラ単体のテスト > Controller Spec

### System Spec

システムテストではFeuture Spec よりSystem Spec が実用的

* テスト終了時にDBの自動ロールバック
* テスト失敗時に自動でスクリーンショット > ターミナルに表示
* `driven_by` でspec 毎にブラウザ切り替えが可能

### Rspec テスト環境

1. RSpec Gem インストール 
2. capybara を`spec/spec_helper.rb` で読み込む
3. FactryBot Gem インストール

```
# RSpec インストール
gem 'rspec-rails', '-> 3.7'
```

```
# spec/spec_helper.rb でcapybara の設定
# capibara 読み込み
require 'capybara/rspec'

RSpec.configure do |config|
  #  ドライバ(ブラウザ)にHeadless Chrome を設定
  config.before(:each, type: :system) do
    driven_by :selenium_chrome_headless
  end
```

```
# FactoryBot インストール
gem 'factory_bot_rails', '-> 4.11'
```

### RSpec 使用用語

* describe: テストの概要 > 外枠
* context: 状況, 状態 > 小見出し
* before: 前提条件 > テストの為の事前準備
* it: 期待する動作 > テスト実行

### FactoryBot でテストデータを作成する

spec/factroies/model_name.rb にテンプレートファイルを作成

```
FactoryBot.define do
  factory :model_name do
    name { 'テストユーザー' }
    email { 'test1@exmple.com' }
    password { 'password' }
  end
end
```

## 6. リソースとセキュリティ

### RESTful

* URL は一つのリソースと考える
* リソースの操作の表現がHTTP メソッドとして統一されている
* 個々のリソースがユニークなURI で表されている
* URL はリソースを表す形, 名詞で表現されている

#### ヘルパーメソッド

* 定義されたURLから生成される対応するアクションを表現する省略形のメソッド名
* URL を抽象化している為, 可読性, メンテナンス性に優れる

#### resources

* 対象とするモデルのデータのCRUD機能のルート一式(7つのアクション)を一括定義できる
* `:only` ,`:except` を使う事で生成するアクションを制限できる

```
#:only で指定したアクションのみ生成
resources :tasks, only: [:index]

#:except で指定したアクション以外を生成
resources :tasks, except: [:delete, :edit, :update]
```
#### resource

対象リソースが一つだけの場合に必要なルートを一括定義できる

### 開発環境で本番環境のエラーを表現

* config/environments/development.rb のconsider_all_requests_local をfalse に変更すればok
* デフォルトでは404, 422, 500 のエラーが用意されている

### Strong Parameters

コントローラでパラメータを受け取る際に、想定通りのパラメータかどうかをホワイトリスト形式でチェックする機能


### マスアサインメント機能

複数の属性の一括代入

```
# 一括代入
> Task = Task.new(name: 'ラーメン食べたい', description: 今すぐ食べたい')
```

### アセットパイプライン

* JavaScript, CSS などのリソースを使いやすくするツール
* sprocket-rails Gem でデフォルトでon になってる
* リソースをコンパイルし、最小化し高速に表示できるようにする

#### 環境による挙動の違い

production 環境, development 環境で挙動が異なる

* development: アセットの結合, 最小化は行われない
* production: アセットの結合が行われJavaScript, CSS のファイルが一つづつ読み込まれた状態になる

### マニフェストファイル

* JavaScript, CSS等のリソースを1つのファイルに結合する内容を決めるファイル
* app/assets/javascripts/application.js, app/assets/stylesheets/application.css 等がこれに当たる
* マニフェストファイルは拡張子は省略する
* scss のマニフェストファイルでは@import のみを利用して記述する

### Capistrano

自動デプロイツール

### プリコンパイル

* production 環境でのアセットのコンパイルを軽減し高速に動作させること
* あらかじめアセットパイプラインを実行し静的ファイルを生成し、リクエスト毎に配信する
* production 環境で必須の作業

### Credintials

* 特定の方式で管理されるproduction 環境用の秘密情報
* 秘密情報は暗号化されてリポジトリに管理できるようにする
* 暗号化の為のキーはアプリケーション外で管理する事で情報の漏洩を防ぐ

## 7. 機能追加

### Ransack

検索機能を追加するGem

```
gem 'ransack'
```

#### Ransack 手順

1. model に追加されたransack メソッドで検索条件追加
2. view に`search_form_for` で検索用フォームを追加(テキスト検索, 日付検索等)
3. 条件によってソート機能なども追加する

### Action Mailer

* メールを送信するRails の標準機能
* 作成するテンプレートを変化させればHTML, Textなど様々な形式で生成できる
* 扱いはコントローラに似ている

#### Action Mailer 手順

1. `rails g` でメイラーを生成
2. テンプレート作成(HTML, Text)
3. コントローラにメール送信処理の実装
4. mailcatcher で動作確認

### mailcatcher

シンプルなsmtp サーバを立てて送信されたメールをブラウザ上で確認できるGem

```
gem 'mailcatcher'
```

* develop 環境ではアプリ側にSMTP サーバの許可を設定しないと使えない
* RSpec にメイラー用のテストも用意されている

### Active Storage

* ファイル管理Gem
* クラウドストレージサービスへファイルをアップロードしてActiveRecord モデルに添付できる
* AWS, Azure 等のクラウドサービスが使用可能
* どんな種類のファイルでも使える

#### Active Storage 手順

1. install(Gem自体はRails 標準でインストール済み)
2. 生成されたファイルでmigrate
3. ファイルを管理する場所, 名前を設定(config/environment/environment/development.rb, config/storage.yml)
4. model, view, controller でそれぞれ設定(StrongParamaters の設定を忘れないこと)

### CSV

* Ruby 標準のCSV ライブラリを使用
* 大量データの一括登録が可能
* 入力, 出力どちらも可能

#### 手順

1. model にクラスメソッド追加 
2. controller でクラスメソッドを呼び出す
3. view にボタン等のインターフェイスを設置

### kaminari

ページネーションを実装するGem

#### kaminari 手順

1. controller index アクションでページ番号がparams[:page] でアクションに渡されるように変更
2. view ページネーションの情報を追加
3. 場合によってlocale の設定(config/locales/kaminari.ja.yml 追加)
4. kaminari のパーシャルテンプレートを使用しデザインの調整

### Active Job

* Rails 標準の非同期処理フレームワーク
* 処理をスケジューリングできる
* Active Job はツールではなくあくまでも仕組み
* 非同期処理を行うには他のツールも必要(Sidekiq, Resque, DelayJob)

### Redis

* キーバリュー型データベース
* Sidekiq を使用する為に必要
* install はbrew 経由でok

### Sidekiq

* 非同期処理用のツール
* brew でmac に直接インストールする
* 使用する為にデータベース(Redis) が必要
* 非同期処理の実行はRedis サーバが起動された状態でなければならない

#### Sidekiq 環境設定

1. Redis install
2. Redis サーバ起動
3. Sidekiq Gem install
4. Sidekiq, Redis の連携設定追加(config/environments/development.rb)
5. Redis サーバ再起動

#### Sidekiq 使用手順

1. `rails g` でジョブのひな形を生成
2. 生成されたファイルにジョブを追記(app/jobs/sample_job.rb)
3. controller でジョブの呼び出しを追記

## 8. JavaScript

Rails + JavaScript でのアプリケーションのパターン

1. Rails でフロントからバックまで実装 + JavaScript でUI のサポート
2. JavaScript でフロント構築 + Rails はサーバサイドのみ(Rails はAPI の提供のみ)

### CoffeeScript

* JavaScriptのコードを生成するためのコンパクトなrubyライクなスクリプト言語
* 自動コンパイル
* app/assets/javascript/ 内にファイルが存在している
* おそらく現在はあまり使われていない

### Turbolinks

* ページ遷移をAjaxに置き換え、JavaScriptやCSSのパースを省略することで高速化するGem
* 遷移先のページをAjax JavaScript とCSS の差分のみを置き換える
* ページ遷移をフックにして動作する
* JavaScript を多く使わないアプリの場合はoff にした方が高速になる
* Rails 4 からデフォルト

### JavaScript でフロント実装の注意点

* app/assets/javascripts/ JavaScript ファイルはアセットパイプライン(Sprockets)で自動的にapplication.js にコンパイルされる
* Turbolinks のon/off で記述が変化する

### Ajax

* ブラウザ上で非同期通信を行いページの再読み込み無しでページを更新する仕組み
* ページ上で変更された差分のみを修正して出力するイメージ

### rails-ujs

* Ajax の送受信の内の送信に関するライブラリ
* Rails 5.1.0 から標準ライブラリになったので意識せずに使用できる

### SJR

* Server-generated JavaScript Responses
* サーバーサイドで生成したJavaScript で画面を更新するプロセスのこと 

### Ajax を使ったページ更新

#### 2つの方法

1. サーバ側で処理を実行 > クライアント側でイベントハンドラを元にJavaScript を実行
2. サーバ側でRails の処理と別にJavaScript の処理も用意する > Rails の処理と同時にJavaScript をクライアント側に送信して実行する

#### 処理の流れ 

1. view のアクションを指定済みのリンクにページジャンプではなくAjax サーバにリクエストを送るように修正(remote: ture オプション)
2. controller でアクション内のredirect_to をHTTP ステータスを204 に変更(head :no_content)
3. アクション後のview の表示をJavaScript で動的に変更する 

### jQuery

* JavaScript ライブラリ
* 他のライブラリ導入に弊害が出るので現在はあまり使われていない

### Yarn

* JavaScript のパッケージマネージャ
* npn より高速でセキュリティが高い
* Rails に同梱されていない
* Rails 6 からwebpacker が標準になりインストールが必須になった
* package.json でパッケージを定義する

### Webpacker

* JavaScript のビルドツールWebpack のラッパーでRails でWebpack を使ってJavaScript を管理することを簡単にしてくれるGem
* Rails 6 から標準になった
* Sprockets との併用は避ける(Webpacker かSprockets どちらかを選ぶ必要がある)

## 9. チーム開発

### database.yml の注意点

* .gitignore を設定しリポジトリに入れない
* .gitignore.yml.example をリポジトリ内に入れコピーして使用する
* パスワード, ユーザ名が記述されているので扱いには十分注意する

### コーディング規約について

RuboCop 等のLintツールを利用して統一する

### マイグレーションはロールバックできるのか確認する

1. `bin/rails db:migrate` でマイグレーションが正常に行われるか確認
2. `bin/rails db:migrate:redo` で以上がないのか確認
3. `redo` で最新の状態に戻っているのでそのまま開発を続ける

### マイグレーションの注意点

* ロールバックできる
* 過去のマイグレーションファイルを変更する事は原則行わない
* マイグレーションは"歴史" という認識の元に積み重ねていくイメージで運用する
* Squasher 等のGem を使う事でマイグレーションファイルをまとめる事ができる
* ローカル、本番などの環境でのマイグレーションファイルの差異がないようにする
* アプリケーションコードに依存しないように記述する(ActiveRecord 等は別にモデルクラスを作成する)
* 大量のレコードを扱うような処理は行わない(find_each を使う)
* 失敗時には例外を発生させてロールバックさせる(update! を使う)
* マイグレーションでActiveRecord を扱う時は`reset_column_information` でスキーマキャッシュを更新する

## 10. リファクタリング

### コードを書く場所について

* model とcontoller を切り分けて記述
* view とmodel を切り分けて記述
* ビジネスロジックはmodel に書く

### Decorator

* ソフトウェアデザインパターンの1つ
* Decorator パターンの機能を提供してくれるGem もある(Draper, ActiveDecorator)

#### Decorator パターンの実装方法

1. モデルオブジェクトをインスタンス変数で保持 > view 用の処理を閉じたクラスを作成して利用
2. view 用の処理をmodule で定義 > view でモデルオブジェクトをextend して機能追加

### モデルの共通化

1. 共通機能のモジュールを複数のモデルにMix-in
2. クラス継承(ApplicationRecordで共通化, STI, 抽象クラス)
3. 共通を担当するオブジェクトを別に作成する

### STI

* Single Table Inheritance(単一テーブル継承)
* 対応するテーブルにtype というカラムが必要(type を元にRails が処理をする為)
* 異なるモデルクラスの間で処理を共通化できる

### コントローラの共通化

1. 共通機能のモジュールを複数のコントローラにMix-in
2. クラス継承(ApplicationController で共通機能)
3. 共通を担当するオブジェクトを別に作成する 

### ビューの共通化

1. partial で共通部品化
2. layouts でアウトライン共通化
3. カスタムヘルパーで共通処理化
4. Decorator パターンでモデルの表示ロジックを分離

### ActiveModel

* ActiveRecord モデルの機能を普通のクラスで使う事をサポートするモジュール郡
* データベースに直接紐付かないようなモデルを作成する事が可能になる

### フォームクラス

* フォーム属性をまとめて関連する処理を担当するクラス 
* 検索機能を追加するRansack など

### 外部API との連携の注意点

* 意味のあるパラメータの集合からクラスを生み出す
* 外部サービスに関するロジックをクラスに閉じ込める

### サービスオブジェクト(サービスモデル)

* 特定の処理に特化したモデル
* ビジネスロジックがコントローラに流れ出すのを防ぐ為に作る
* モデル名で処理の内容が分かりやすくなる
* モデル内のクラス名は動詞, またはClassNameService のようなServiceで終わるクラス名

### サブリソース

* RESTful なアクション一式(index, show, new, edit, create, update, destroy)以外のアクション
* サブリソースのみをまとめて小型のコントローラを作成する事でメインのコントローラをシンプルにできる
* メインのコントローラに2,3回アクションの追加をするなら別のコントローラを作成した方が良い

### モジュールを使う注意点

* 意味のまとまりに注目してモジュールを切り出す
* Mix-in 先で違和感がないか(モジュール名, 処理内容)
* 破壊的な処理は追加しない
* 独立的に作成し、他のモジュールに依存しないように
* 利用条件などをコメントで分かりやすく

 
