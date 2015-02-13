項目77 インスタンス制御に対しては、readResolveよりenum型を選ぶ
==============================================================

コンストラクタでのアクセス制限
------------------------------

### シングルトンでアクセス制限

項目３ではシングルトンを作るのにコンストラクタでアクセス制限をしていた

```Java
public class Doraemon {
    public static final Doraemon INSTANCE = new Doraemon();

    private Doraemon() {
    }

    public static Doraemon getInstance() {
        return INSTANCE;
    }
}
```

### Serializableでシングルトン

implements Serializable されると
readObjectが新たなインスタンスを作ってしまう..orz

なのでこうする
```Java
private Object readResolve() {
    // staticに格納してた　どらえもんを返却する
    // 新たに作られたどらえもんはガーベッジコレクタが始末！
    return INSTANCE;
}
```

> readResolve メソッドを使うことによって、呼び出し側に返される前に、
> ストリームから読み込んだオブジェクトを置換または解釈処理できます
> https://docs.oracle.com/javase/jp/1.3/guide/serialization/spec/input.doc6.html


こうするとディシリアライズされたオブジェクトを無視するので
Doraemonインスタンスのシリアライズデータはなんもデータがいらない。

### すべてのフィールドに *transient* を宣言すべし！

> フィールドの定義にtransientを付けると、
> Serializableをimplementsしたクラスであっても
> そのフィールドはシリアライズの対象外になる。
> http://www.ne.jp/asahi/hishidama/home/tech/java/serial.html

*なんでそんなことするの？？*
⇒readResolveメソッドが実行される前にtransientフィールドが
デシリアライズされるのでその間に盗むことができる！らしい

// TODO あとで参照を盗む方法をまとめる

enum型にする方がいいよ
----------------------

enumにすればJVMが保障してくれるよ！

```Java
public enum EnumDoraemon {
    INSTANCE;

    private String[] favoriteFoods = {
            "Dora Yaki", "Tako Yaki"
    };

    public void sayFavorites() {
        System.out.println(
                Arrays.toString(favoriteFoods)
        );
    }
}
```

でもコンパイル時にインスタンスが
分からなくてシリアライズしたい場合には
enumは使えないよ。

補足：readResolveのアクセス可能性に注意
---------------------------------------

publicとかでも可能だけど...
> ANY-ACCESS-MODIFIER
> https://docs.oracle.com/javase/jp/1.3/guide/serialization/spec/input.doc6.html

finalでないクラスは要注意。

サブクラスがディシリアライズ時に
スーパークラスのreadResolveが呼ばれ
ClassCastExceptionになってしまう

まとめ
--------
- インスタンスが変わらないこと(シングルトン)を強制するにはなるべく enumにする
- コンパイル時にインスタンスが分かってなくシリアライズ可能なシングルトンは
transientとreadResolveを使おう
