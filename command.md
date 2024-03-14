# コマンド備忘録
```
php artisan make:model ContactForm -ms
```
1. 「contact_forms」というテーブルが作成される。※表記はスネークケースとなり、接尾辞にsがつく
2. /database/migrations/配下にスキーマ定義用のファイルが作られる

既存のテーブルを更新するコマンド
````
php artisan make:migration add_title_to_contact_forms_table
````
- add_title_to_[DBのテーブル名]_tableと記載する
- /database/migrations/配下にスキーマ定義用のファイルと別で、ファイルが作られる


````
php artisan migrate:status
````
実行履歴がみれる

````
php artisan serve
````
サーバの起動

````
php artisan migrate:fresh --send
````
DBのテーブルの中身を空にして、seederファイルを実行する
````
php artisan make:model Shop -ms
````
modelクラスのファイルを作成
migrationファイル作成
````
php artisan route:list
````
現在のルーティングの状態が見れる













composer create-project --prefer-dist laravel/laravel task_test "9.*.*"
