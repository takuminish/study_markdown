#RailsにmySQLを導入する方法

##・rootでログイン
コンソールで`mysql -u root -p`と入力

##・ ユーザ一覧の取得
コンソールで`select User,Host from mysql.user;`と入力することで表示

##・ 新しくユーザを作る
rootユーザで操作を行う

`CREATE USER 'your_name'@'localhost' IDENTIFIED BY 'your_password';`
(your_nameとyour_passwordは自分で決めたもの)を実行することでユーザが作成できる

## ・ 作成したユーザに権限を付与
mysqlコンソールにて`grant all on *.* to '[ユーザー名]'@'localhost'`を実行する
## ・ RailsにmySQLを導入
Railsアプリ作成時に`rails new アプリケーション名 -d mysql`とするとDBにmySQLが使用されるようになる

## ・ database.ymlの設定
/config/database.yml内のusernameとpasswordをそれぞれ作成したユーザに変更

## ・ DBを作成してみる
Railsアプリ用のDBを作成する。`rake db:create:all`を実行することでRails用のDBが作成される。これを実行しておかないと、テーブル作成時にエラーとなる。
Userモデルを作成する。UserモデルはString型のnameとString型のpassを持つようにする。
コンソールで`rails g model User name:string pass:string`
これによりマイグレーションファイルとやらが作成される。次に`rake db:migrate`を実行することでtableが作成される

## ・ tableを確認する
コンソールにて`mysql -u ユーザ名`を実行し、mysqlを起動する。
`show database;`により現在作成されているDBが表示される。
`show tables from DB名;`によりDB名に作成されているテーブルが表示される。
`show columns from テーブル名;`によりテーブルの構造が表示される。
