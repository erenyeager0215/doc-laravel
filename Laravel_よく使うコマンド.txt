

DBテーブル作成時コマンド

php artisan make:model ContactForm -m
「contact_forms」というテーブルが作成される（スネークケースとなり、接尾辞にsがつく）
/database/migrations/配下にスキーマ定義用のファイルが作られる

既存のテーブルを更新するコマンド

php artisan make:migration add_title_to_contact_forms_table
add_title_to_[テーブル名]_tableと記載する
/database/migrations/配下にスキーマ定義用のファイルと別で、ファイルが作られる


php artisan migrateで migrateファイルの実行？

php artisan migrate:status
実行履歴がみれる

php artisan serve

php artisan migrate:fresh --send

php artisan make:model Shop -ms

php artisan route:list

composer create-project --prefer-dist laravel/laravel task_test "9.*.*"
