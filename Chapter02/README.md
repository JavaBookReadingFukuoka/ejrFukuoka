# 第２章 オブジェクトの生成と消滅

## 項目1、項目2
### スライド
[Effective Java輪読会 vol0](http://www.slideshare.net/leecgeun/effective-javavol0)

## 項目3

## 項目4 privateのコンストラクタでインスタンス化不可能を強制する
Utilityクラスなどクラスメソッドのみでインスタンス化が不要なものはコンストラクタを明示的にprivateにして不用意にデフォルトコンストラクタが呼び出されることを防ぐ。

```java
public class UtilityClass {
  //インスタンス化できないようにprivateで宣言
  private UtilityClass() {}
 
 /*
 以下省略
 */
}
```

このコンストラクタを記述するときは直感的ではないため、コメントでその旨を明記する。

この手法の副作用としてサブクラスの作成を防止する。継承したクラスのコンストラクタはスーパークラスのコンストラクタにアクセスできなければならないが、privateなのでアクセすることができない。


## 項目5 不必要なオブジェクトの生成を避ける
### オブジェクトが不変であれば再利用できる
#### 悪い例

```java
String s = new String("hogehoge");
```

#### 理由
機能的に同じオブジェクトが必要な時、新しく生成するより使いまわしたほうが良い。
オブジェクトが不変であれば再利用ができる。
この文は実行毎に新たにインスタンスを生成する。"hogehoge"はそれ自身がStringインスタンスなのでnew Stringする必要はない。
この文がループ内にあった時などはインスタンス生成がループの回数だけ行われる。

#### 改善

```java
String s = "hogehoge";
```

このコードはひとつのStringインスタンスを使いまわす。
また同じ文字列リテラルなら同一仮想マシンの他コードでもオブジェクトが使いまわされることが保証される。

### staticファクトリーメソッドの使用
staticファクトリーメソッド(項目1)とコンストラクタを両方提供している不変クラスは、staticファクトリーメソッドの方を使用することで重複したオブジェクトの生成を回避できる。

### 例

```java
Boolean.valueOf(String)
Boolean(String)

```

この2つでは前者のほうが望ましい。コンストラクタは呼び出される毎に新たにオブジェクトを生成する。

### 可変オブジェクト(immutable object)の再利用
#### 悪い例
```java
public class Person {
/* 省略 */

  //やってはいけない
  public boolean isYutori() {
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1987, Calendar.APRIL, 1, 0, 0, 0);
    Date yutoriStart = gmtCal.getTime();
    gmtCal.set(2004, Calendar.APRIL, 1, 0, 0, 0);
    Date yutoriEnd = gmtCal.getTime();
    
    return birthDate.compareTo(yotoriStart) >= 0 && birthDate.compareTo(yotoriEnd) <0;
  }
}
```

#### 理由
このメソッドは呼び出される毎にCalendarインスタンスやDateインスタンスを生成する。

#### 改善
```java
public class Person {
/* 省略 */

  private static final Date YUTORI_START;
  private static final Date YUTORI_END
  
  static {
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1987, Calendar.APRIL, 1, 0, 0, 0);
    YUTORI_START = gmtCal.getTime();
    gmtCal.set(2004, Calendar.APRIL, 1, 0, 0, 0);
    YUTORI_END = gmtCal.getTime();
  }

  public boolean isYutori() {
    return birthDate.compareTo(YUTORI_START) >= 0 && birthDate.compareTo(YUTORI_END) <0;
  }
}
```

YUTORI_START、YUTORI_ENDはクラス初期化時に一度だけ生成される。また副次的な降下として定数であることが明白になる。
メソッドを使わない時もインスタンスが生成されてしまう。これは遅延初期化(項目71)で回避することはできるが実装が複雑になる割に降下は大きくないのでおすすめできない。

### 再利用できるのが明白でない場合(アダプターパターンについて)
#### 例
MapインターフェースのKeySetメソッドは同じSetオブジェクトを返しても、返されたオブジェクトは機能的に同じ為、一つが変更されたら他のオブジェクトすべてが変更される。
(同じMapインスタンスにバックアップされているため？)

### オートボクシングに注意
J2SE1.5以降ではプリミティブ型とボクシングされたオブエクとの違いが不明瞭

longをLongとtypoするだけで自動でボクシングされ意図せずにオブジェクトが生成される。
ボクシングされた基本データ型より基本データ型を選ぶ。意図しないオートボクシングに注意

### オブジェクト生成はコストが掛かり避けるべきだというわけではない
最新のJVMでは小さにオブジェクトを生成・回収することにコストは掛からない。明瞭性や簡潔性のためには一般的に良いこと
オブジェクトプールで管理するのはDBコネクション等生成が重いもののみ、一般的にはオブジェクトプールは乱雑になるため避ける。

## 項目6 廃れたオブジェクト参照を取り除く
### JavaはGCがあるが自分でメモリ管理しなければならない場合もある
例えばスタックを配列で実装した時、ポップしたオブジェクトの参照が入っていたところ(廃れた参照)はちゃんとnullで参照外してあげないとGCに回収されない。
null入れてたらなにか間違って取り出した時にぬるぽ出るから不正に気づくよ。
ただ何でもかんでもnull設定していたらだめで、nullを使うのは例外であるべき

廃れた参照を排除するいい方法は変数をスコープの外にだすこと。項目45にあるようにスコープを最小限に務めることで自然に実現される。

### nullを使うべきところ
クラスが独自にメモリ管理している時、メモリリークに注意する。

## 項目7
