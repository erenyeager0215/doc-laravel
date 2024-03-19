引数が文字列の場合その文字列が
`xxx[hoge]`
のような書き方をした場合は、グローバル変数からは当該
xxx がキーとなり、配列を返す

`xxx[hoge]`は JS でいう`xxx.hoge`と同義

例）

```
<form method="post" action="some_script.php">
    <input type="text" name="data[one]" value="Value 1">
    <input type="text" name="data[two]" value="Value 2">
    <input type="submit" value="Submit">
</form>
```

このフォームが送信されると、$\_POST['data']は以下のような配列としてアクセス可能になります

```
Array
(
    [one] => Value 1
    [two] => Value 2
)
```

php ではよく`$_POST['data'][one]`と書くことがあるがこれも
JS でいう`$_POST.data.one`と同義
