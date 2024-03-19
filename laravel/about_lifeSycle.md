# ライフサイクルの流れ

0. クライアントがリクエスト
1. index.php
2. autoloudファイルを読み込み
3. applicationの設定や機能の読み込み
4. カーネルの作成
5. リクエスト及びレスポンス
6. レスポンス後にクリーンアップし、カーネルの破棄



# １．すべてのリクエストの入り口はindex.phpへ

これは web サーバの設定で public/.htaccess ファイル内にデフォルトで下記のように設定しているため

```php:public/.htaccess
// Send Requests To Front Controller...
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [L]
```

# ２．index.php の中身

## １．autoload ファイルの読み込み
他のファイルを読み込みを楽ちんにする

````php:index.php
require __DIR__.'/../vendor/autoload.php'; // 【１】autoload読み込み
````

requireを使ってファイルを読み込む必要がありますが、
このautoloadを最初に読み込むことでいちいち
requireでファイルを読み込むことなく別ファイルのクラスを利用できる
autoloadはcomposerの機能の一部。composerが生成するautoload.phpファイル
で●●ディレクトリ配下はrequire無しでファイル読み込みができるように設定できる

## ２．Application インスタンス作成
サービスコンテナの機能等が読み込まれる
```php:index.php
$app = require_once __DIR__.'/../bootstrap/app.php'; // 【２】Applicationインスタンス作成
```

bootstrap/app.phpの中身

```php:bootstrap/app.php
$app = new Illuminate\Foundation\Application(
   $_ENV['APP_BASE_PATH'] ?? dirname(__DIR__)　//環境変数が設定されていない場合はルートディレクトリを返す
);

return $app;
```
require_onceなので一回だけ呼び出される。
このApplicationインスタンスは、アプリケーション全体で使用されるサービスコンテナ（依存関係注入コンテナ）の役割を果たし、アプリケーションのサービスプロバイダを登録し、アプリケーションのライフサイクルを管理します。


## ３. HttpKernel インスタンス作成
カーネルを作成
```php:/vendor/laravel/framework/src/Illuminate/Foundation/Http/Kernel.php
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class); // 【３】HttpKernelインスタンス作成
```

## ４. Request インスタンス作成
リクエストデータからRequestインスタンスを作成
```php:index.php
$response = $kernel->handle(
   $request = Illuminate\Http\Request::capture() // 【４】Requestインスタンス作成
);
```

capture()をさかのぼると、$_GETや$_POSTなどスーパーグルーバル変数の中身を受け取り、
それらの値でリクエストクラスがnewされ、リクエストインスタンスが返ってくる処理

※詳細
https://qiita.com/minato-naka/items/f05b023950ce544a8bec#%EF%BC%94request%E3%82%A4%E3%83%B3%E3%82%B9%E3%82%BF%E3%83%B3%E3%82%B9%E4%BD%9C%E6%88%90

## ５.HttpKernelがリクエストを処理してResponse取得
Requestインスタンスをもとに、MVCの処理をしクライアントへレスポンスする
```php:index.php
$response = $kernel->handle( // 【５】HttpKernelがリクエストを処理してResponse取得
      $request = Illuminate\Http\Request::capture()
);
```
kernel->handle($request)の中身

```php:index.php
public function handle($request){
   $response = $this->sendRequestThroughRouter($request);
   return $response;
}
```

さらにsendRequestThroughRouterの中身

```php:/vendor/laravel/framework/src/Illuminate/Foundation/Http/Kernel.php
protected function sendRequestThroughRouter($request){
   $this->bootstrap();
   return (new Pipeline($this->app))
      ->send($request)
      ->through($this->middleware)
      ->then($this->dispatchToRouter());
}
```

$this->bootstrap();
この行は、アプリケーションが適切に機能するために必要な初期化処理を行う。
これには、環境設定のロード、サービスプロバイダの登録、ファサードの初期化などが含まれる。
アプリケーションのコアコンポーネントが適切に設定され、
リクエスト処理の準備が整う。


->send($request)
処理するHTTPリクエストをパイプラインに送信
->through($this->middleware)
ミドルウェアを通過
->then($this->dispatchToRouter());
ルータへリクエストをディスパッチ

この後、MVCアークテクチャの通り、routerからcontrollerへ渡され、
処理が終わったら、responseを返す。
そのresponseは最終的に、
$response = $kernel->handle($request = Request::capture())->send();
でいう、$kernel->handle()->send()でクライアントへ送信される
