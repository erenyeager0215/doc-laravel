「static::の使い方とselfの使い方」

class ParentClass {
    public static function who() {
        echo __CLASS__;
    }

    public static function testSelf() {
        self::who();
    }

    public static function testStatic() {
        static::who();
    }
}

class ChildClass extends ParentClass {
    public static function who() {
        echo __CLASS__;
    }
}

ParentClass::testSelf();  // 出力: ParentClass
ParentClass::testStatic(); // 出力: ParentClass

ChildClass::testSelf();   // 出力: ParentClass
ChildClass::testStatic(); // 出力: ChildClass

self:: は静的メンバーにアクセスする際に、常にそのコードが書かれているクラスを指します。
static:: は実行時のクラスを指し、サブクラスでのオーバーライドを考慮した動的な振る舞いを可能にします。