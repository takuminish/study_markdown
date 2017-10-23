# Railsでdeviseを使用する方法

## ・ deviseのインストール
`emacs Gemfile`でGemfileを開く。Gemfileに`gem 'devise'`を記述する。
次に`bundle install`を行う。
bundle installが完了すれば`rails g devise:install`を行い、deviseをインストールする。

## ・ deviseの設定
### ・ deviseのメール送信時のホスト名を指定
config/environment/development.rbに`config.action_mailer.default_url_options = { host: 'localhost:3000' }`を追加

### ・ root_urlを指定
deviseはログアウトじにroot_urlに繊維するためroute.rbにroot_urlを記述しないといけない。
config/route.rbに`root to: "コントローラー名#アクション名"`を記述する。今回はhoem#indexとする。

### ・ エラーメッセージの表示領域を作成
deviseはログインやログアウト時にflashにメッセージを追加するためその表示を追加します。
app/views/layouts/application.erbに
`<p class="notice"><%= notice %></p>`
`<p class="alert"><%= alert %></p>`を追加する。

### ・ deviseのViewを作成する
`rails g devise:views`を実行することでdeviseのViewを作成することができる。

## ・ deviseを使用したモデルの作成
今回はUserというモデルを作成する。`rails g devise User`を実行することでモデルが作成される。

## ・ モデルの設定を変更
deviseで作成されたモデルはemailとpasswordによって認証される。しかし今回はnameとpasswordで認証を行うため、設定を変更する。
### ・ db/migrate/マイグレーションファイルの変更
db/migrate/マイグレーションファイルを開く
`t.string :email, null: false, default: ""`を`t.string :name,null: false, default:""`
`add_index :users, :name ,unique: true`を`add_index :users, :name , unique: true`に変更する。

### ・ app/controller/application.rbの変更
app/controller/application.rbに以下を追加する。

      before_action :configure_permitted_parameters, if: :devise_controller?

      protected

      def configure_permitted_parameters
          devise_parameter_sanitizer.permit(:sign_up) do |u| 
              u.permit(:name, :password, :password_confirmation)  
          end
    　　   devise_parameter_sanitizer.permit(:sign_in) do |u| 
              u.permit(:name, :password, :remember_me) 
          end
      end

#### ・ app/model/user.rbの変更
app/models/user.rbに以下を追加する。

      validates_uniqueness_of :name
      validates_presence_of :name


      def self.find_first_by_auth_conditions(warden_conditions)
        conditions = warden_conditions.dup
        if login = conditions.delete(:login)

          where(conditions).where(["name = :value", { :value => name }]).first
        else
          where(conditions).first
        end
      end

      def will_save_change_to_email?
        false
      end
      def email_required?
        false
      end

      def email_changed?
        false
      end
      
### ・ config/initializers/devise.rbの変更
`config.authentication_keys = [:email]`のコメントアウトを解除し、`[:email]`を`[:name]`に変更する。
`config.case_insensitive_keys = [:email`と`config.strip_whitespace_keys = [:email]`の`[:email]`を`[:name]`に変更する。

### ・ テーブルの作成
`rake db:migrate`によりテーブルを作成する。

## ・ Viewの変更
モデルを変更したことによりViewも変更しなければならない。
### ・ app/views/devise/sessions/new.html.erbの変更
emailを入力するフォームをユーザネームを入力するフォームに変更する。

    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true %>

を

    <%= f.label :name %><br />
    <%= f.text_field :name, autofocus: true %>
    
に変更する。

### ・ app/views/devise/registrations/new.html.erbの変更
emailを入力するフォームをユーザネームを入力するフォームに変更する。

    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true %>

を

    <%= f.label :name %><br />
    <%= f.text_field :name, autofocus: true %>
    
に変更する。

## 参考ページ
- [Railsのログイン認証gemのDeviseのカスタマイズ方法 - Rails Webook](http://ruby-rails.hatenadiary.com/entry/20140804/1407168000)
- [Rails deviseによるユーザー認証 メールによる認証、emailとusernameのどちらでもログイン可能にするまで - Qiita](https://qiita.com/shizuma/items/c8c2e71af8c1dcf3d1c2)
- [deviseをユーザー名で登録・ログインできるように修正する - Qiita](https://qiita.com/dayone80/items/ce04b21690f5f50a5cfa)

