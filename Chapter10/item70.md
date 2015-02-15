# 70 スレッド安全性を文書化する

APIを安全にマルチスレッドで使えるように、クラスはそれをサポートするスレッド安全性のレベルを明確に文書化すること！！

+ 不変（immutable）：変更不可能なら安心して参照できるね！
+ 無条件スレッドセーフ（unconditionally thread-safe）：内部実装がうまいことやってくれるよ！
+ 条件付きスレッドセーフ（conditionally thread-safe）：安全に使うには書いてあることを守ろう！
+ スレッドセーフでない（not thread-safe）：危ない！自前で同期制御しないと！
+ スレッド敵対（thread-hostile）：複数スレッドから参照するのはあきらめろ！！

上記はJCiPのスレッド安全性アノテーションと対応する。
+ [@Immutable](http://jcip.net.s3-website-us-east-1.amazonaws.com/annotations/doc/net/jcip/annotations/Immutable.html)
+ [@ThreadSafe](http://jcip.net.s3-website-us-east-1.amazonaws.com/annotations/doc/net/jcip/annotations/ThreadSafe.html)
+ [@NotThreadSafe](http://jcip.net.s3-website-us-east-1.amazonaws.com/annotations/doc/net/jcip/annotations/NotThreadSafe.html)

## 条件付きスレッドセーフを文書化する場合の注意
必要なロックについて示すこと！<br>
たいていはインスタンス自身だが、例外もある。<br>
例：
```
Map<K, V> m = Collections.synchronizedMap(new HashMap<K, V>());
Set<K> s = m.keySet();
synchronized(m) { // SetでなくMapに対してロック取得！
    for (K key : s)
        key.f();
}
```

## プライベートロックオブジェクト
```
private final Object lock = new Object();
public void foo() {
    synchronized(lock) {
        ...
    }
}
```
上記のプライベートロックオブジェクトはカプセル化されておりサービス拒否攻撃を阻止できて便利。<br>
だが、条件付きスレッドセーフでは使わないよう注意！！

## 余談
+ synchronizedを探して判別できると思うな、きっちり設計して文書化しろ
+ スレッド敵対のクラスは殆どないから安心していい。[System.runFinalizersOnExit](http://docs.oracle.com/javase/jp/8/api/java/lang/System.html#runFinalizersOnExit-boolean-)は非推奨だ
+ Threadクラスがプライベートロックオブジェクトでやらかした事があるらしい？＞[これか？](http://www.javaspecialists.eu/archive/Issue144.html)
