「サービスコンテナ」
・クラスをインスタンス化してくれるマシーン

//サービスコンテナでインスタンス化した場合
$classA = app()->make(ClassA::class);

1.app()関数でサービスコンテナを取得
2.サービスコンテナのmake()メソッドでインスタンス化
※$classA は new ClassA(); と同じ中身

サービスコンテナの機能
【１】依存解決してくれる
【２】インスタンス化の方法をカスタマイズできる


【１】依存解決

クラスAのコンストラクタの引数にクラスBの型宣言（タイプヒンティング）していた場合、
クラスAをインスタンス化した際に、自動でクラスBのインスタンス化をしてくれる

class ClassA
{
    public function __construct(ClassB $classB)
    {
        \Log::info('ClassAインスタンス化完了');
    }
}

class ClassB
{
    public function __construct()
    {
        \Log::info('ClassBインスタンス化完了');
    }
}

$classA = app()->make(ClassA::class);
// local.INFO: ClassBインスタンス化完了  
// local.INFO: ClassAインスタンス化完了  

サービスコンテナの動き
１．「ClassAをインスタンス化するぞ」
２．「と思ったら、コンストラクタでClassBが型宣言されてるな」
３．「じゃー先にClassBをインスタンス化しよう」
４．「ClassBのインスタンス化完了！」
５．「このClassBインスタンスを引数に渡して、ClassAをインスタンス化しよう」
６．「ClassAインスタンス化完了！」
※これは再帰的に行われるため、仮にClassBのコンストラクタの引数にClassCが定義されてたら、
ClassCのインスタンス化がされた後に当該インスタンスがClassBの引数に返される


詳細は下記の「依存解決」を参考にする
https://qiita.com/minato-naka/items/afa4b930a2afac23261b


【２】インスタンス化の方法のカスタマイズ

//インスタンス化方法のカスタマイズを定義するには、
サービスコンテナのbind()メソッドを仕様

app()->bind('呼び出しキーワード', 'インスタンス化時の処理');


// カスタマイズ方法定義
app()->bind(ClassX::class, function () {
    return new ClassY();
});

// 「ClassX::class」キーワードでインスタンス化
$classX = app()->make(ClassX::class);

キーワード「ClassX::class」で呼ばれたら、
ClassYのインスタンスを返す。

サービスコンテナが「キーワード」と「インスタンス化時の処理」の対応表を持っていて、
make()で呼び出されるたびに
その対応表を確認してインスタンス化してくれている

interfaceを呼び出しキーワードとしたとき、
app()->bind(CarInterface::class, function () {
    return new CarClass();
});

//インターフェース名と具象クラスの対応を定義する場合は
下記のような省略記述が可能
app()->bind(CarInterface::class, CarClass::class);


サービスコンテナのbind(結合)の定義は一般的にサービスプロバイダ
（app/Providers/AppServiceProvider.php）に記載する

当該サービスプロバイダはLaravel起動時に最初に実行される仕組み