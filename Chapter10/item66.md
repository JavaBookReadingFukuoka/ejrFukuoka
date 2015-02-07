# 66 共有された可変データへのアクセスを同期する

## synchronized予約語について。
１つのブロックまたはメソッドを１つのスレッドだけが実行することを保証する。
```
public synchronized foo() {
	//１つのスレッドがfoo()を実行中別のスレッドはfoo()を実行できない
}
public bar() {
	System.out.println("synchronizedブロックの前");
	synchronized (obj) {
		//１つのスレッドがこのブロックを実行中別のスレッドはこのブロックの処理を実行できない。
	}
	System.out.println("synchronizedブロックの後");
}
```

## １つの変数の読み書き
+ long、double以外の場合＝＞atomicである
+ long、doubleの場合＝＞atomicでない<br>（[17.7. Non-atomic Treatment of double and long](http://docs.oracle.com/javase/specs/jls/se7/html/jls-17.html#jls-17.7)）によると、non-bolatileなlongまたはdoubleの場合、Javaのメモリモデルでは64bitのうち32bitを最初に書き込んだ後、残りの32bitを書き込むらしい）

## 変数の読み書きがatomicだからといって、同期をさぼってはダメ
+ VM最適化により書いたコードと実行されるコードが変わるケースがある
+ 書き込み操作と読み込み操作の両方に対して、適切に同期処理を実装するべし
+ volatileを使うことも出来る

## volitile
+ フィールドを読み込むスレッドが、最後に書き込まれた値を見えることを保証する
+ [volatileとは：SJC-P対策Java用語集](http://sjc-p.obx21.com/word/ev/volatile.html)
+ synchronizedを使うよりもパフォーマンスが良くなり、コード量も少なくなるが、うまく使うのは難しい<br>
採番処理に使おうとするとBAD！

## [volatile を正しく使うためのパターン](http://www.ibm.com/developerworks/jp/java/library/j-jtp06197.html)
+ ステータス・フラグ(シャットダウン要求など)
+ １度だけ安全に公開する
+ 独立した観測結果
+ volatile bean


##まとめ
+ そもそも可変データを全く共有しなければ無問題！！
+ 不変データを共有しよう
+ 事実上不変なデータを共有しよう
+ 複数のスレッドが可変データを共有する場合には、そのデータを読み書きするスレッドは同期を行わなければならない


## 余談
+ スレッドの停止にThread.stop()を使ってはダメ。本質的に安全ではない。<br>
  [Java 非推奨スレッドプリミティブ(Oracle)](http://docs.oracle.com/javase/jp/7/technotes/guides/concurrency/threadPrimitiveDeprecation.html)<br>
  [THI05-J. スレッドの強制終了にThread.stop()メソッドを使用しない(JPCERT)](https://www.jpcert.or.jp/java-rules/thi05-j.html)
+ 加算演算子（++）はatomicでない
+ 採番処理では[java.util.concurrent.atomic.AtomicLong](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/atomic/AtomicLong.html)を使うのが良い
