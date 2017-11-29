# Railsでrubocopを使用する方法

## Gemのインストール
Gemfileに`gem 'rubocop', require: false`を追加する。
`bundle install`を実行する。

## rubocopの各種設定(設定を使用するたびに追加していく)

### Include:
Includeはrubocopのチェックを行うファイルを指定するもの

	Include:
      - ''**/Rakefile

この場合はRakefileがrubocopのチェックの対象となる。

### Exclude:
Excludeはrubocopのチェックを行わないファイルを指定するもの

	Exclude:
      - 'db/**/*'
      
この場合はdb内のすべてのファイルがrubocopのチェックの対象外となる。

## rubocop_todo.yml
`rubocop --auto-gen-config`を行うことで、現在offensesとなっているエラーをrubocop_todo.ymlに保存することができる。
`.rubocop.yml`内で`inferit_from: .rubocop_todo.yml`とすることで、エラーをいったん無視することができる。
この機能により、rubocopのルールは`.rubocop.yml`に記述し、これから直したいルールは`.rubocop_todo.yml`に記述することができる。

## 参考ページ
- [Railsメモ(22) : RuboCopで静的コード解析を行う - もた日記 - wonder-wall](http://wonderwall.hatenablog.com/entry/2015/08/17/213718)
