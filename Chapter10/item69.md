# 69 waitとnotifyよりコンカレンシーユーティリティを選ぶ

## 最初に結論
[wait](http://docs.oracle.com/javase/jp/8/api/java/lang/Object.html#wait--)と[notify](http://docs.oracle.com/javase/jp/8/api/java/lang/Object.html#notify--)を正しく使用するのは平凡なプログラマには難しすぎるので、Concurrency Utilitiesを使おう。

## Concurrency Utilitiesとは？
+ エグゼキューターフレームワーク（項目68で説明）
+ コンカレントコレクション
+ シンクロナイザー

## コンカレントコレクション
+ [ConcurrentHashMap](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ConcurrentHashMap.html) : 取得の完全な同時性および予想される高い更新平行性をサポートするハッシュ・テーブル
+ [ConcurrentSkipListMap](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ConcurrentSkipListMap.html) : スケーラブルな並行ConcurrentNavigableMap実装
+ [ConcurrentSkipListSet](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ConcurrentSkipListSet.html) : ConcurrentSkipListMapに基づくスケーラブルな並行NavigableSet実装
+ [CopyOnWriteArrayList](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/CopyOnWriteArrayList.html) :基になる配列の新しいコピーを作成することにより、すべての推移的操作(add、setなど)が実装されるArrayListのスレッド・セーフな変数
+ [CopyOnWriteArraySet](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/CopyOnWriteArraySet.html) : 内部のCopyOnWriteArrayListをすべてのオペレーションで使用するSet
など。<br>
これらは、独自の同期を内部的に管理している……そのため外部からのロックは無意味！！

### 使い方の例１（ConcurrentHashMap）
```
private static final ConcurrentMap<String, String> map = new ConcurrentHashMap<>();
public static String intern(String s) {
    String result = map.get(s);
    if (result == null) {
        result = map.putIfAbsent(s, s);
        if (result == null) {
            result = s;
        }
    }
    return result;
}
```
putIfAbsent(key, value)はキーに対するマッピングが無ければマッピングを挿入してnullを返し、
マッピングが存在していれば、キーに既に関連づけられた値を返す。<br>
やむを得ない理由が無い限り、Collections.synchronizedMapやHashtableよりはConcurrentHashMapを使え！<br>
（※もちろんシングルスレッドならHashMapだと思う）


### 使い方の例２（CopyOnWriteArrayList）
```
private final List<SetObserver<E>> observers = new CopyOnWriteArrayList<>();
public void addObserver(SetObserver<E> observer) {
    observers.add(observer);
}
public boolean removeObserver(SetObserver<E> observer) {
    return observers.remove(observer);
}
private void notifyElementAdded(E element) {
    for (SetObserver<E> observer: observers)
        observer.added(this, element);
}
```
P260の項目67のコード。
すべての書き込み操作は実際の配列全体の新たなコピーを生成することで実装されている。

## キュー
putとtakeのブロック・バージョンを定義する拡張された[BlockingQueue](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/BlockingQueue.html)インタフェースが活躍？
+ [LinkedBlockingQueue](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/LinkedBlockingQueue.html) : リンク・ノードに基づく、オプションで制限付きになるブロッキング・キュー（FIFO）
+ [ArrayBlockingQueue](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ArrayBlockingQueue.html) : 配列に連動する、制限付きのブロッキング・キュー（FIFO）
+ [SynchronousQueue](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/SynchronousQueue.html) : 各挿入オペレーションが別のスレッドによる対応する削除オペレーションを待機する必要がある(およびその逆の) ブロッキング・キュー
+ [PriorityBlockingQueue](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/PriorityBlockingQueue.html) : クラスPriorityQueueと同じ順序付けルールを使用するとともにブロッキング取得オペレーションを提供する、制限なしのブロッキング・キュー
+ [DelayQueue](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/DelayQueue.html) : Delayed要素の制限なしのブロッキング・キューで、遅延時間が経過後にのみ、要素を取得可

BlockingQueueには待ち合わせるメソッドがある。
+ [void put(E e)](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/BlockingQueue.html#put-E-) : 指定された要素をこのキューに挿入します。必要に応じて、空きが生じるまで待機します。
+ [E take()](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/BlockingQueue.html#take--) : このキューの先頭を取得して削除します。必要に応じて、要素が利用可能になるまで待機します。

## シンクロナイザー
スレッドが他のスレッドを待つことを可能にするオブジェクト
+ [Semaphore](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Semaphore.html) : 従来の並行処理ツール。計数セマフォ
+ [CountDownLatch](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/CountDownLatch.html) : ほかのスレッドで実行中の操作セットが完了するまで、1つ以上のスレッドを待機可能にする同期化支援機能
+ [CyclicBarrier](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/CyclicBarrier.html) : スレッド・セットのそれぞれが共通のバリアー・ポイントに達するまで待機することを可能にする同期化支援機能
+ [Phaser](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Phaser.html) : 再使用可能な同期化バリアー
+ [Exchanger](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Exchanger.html) : スレッドをペアにして、ペア内の要素を交換できる同期ポイント

### 使い方（CountDownLatch）
（あとで）

## 仕方なくwaitメソッドを使用する場合
必ずwaitループイディオムを使うこと！！ループの外で決して呼び出してはならない！！
```
// waitメソッドを使用するための標準イディオム
synchronized (obj) {
	while (<条件が成立していない>) {
		obj.wait(); // (ロックを解放、そして、起きたときに再取得)
		// 条件に対して適切な処理を行う
	}
}
```

## 余談
最近のさくらばさんのJava8の記事は、[CompletableFuture](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/CompletableFuture.html)の話でした。非同期に処理する複数のタスクを組み合わせて実行することができる模様。どっちかというと、項目68の発展だったかも。
+ [詳解 Java SE 8 第19回 Concurrency Utilitiesのアップデート その1](http://itpro.nikkeibp.co.jp/atcl/column/14/224071/010400014/)
+ [詳解 Java SE 8 第19回 Concurrency Utilitiesのアップデート その2](http://itpro.nikkeibp.co.jp/atcl/column/14/224071/010400015/)
+ [詳解 Java SE 8 第19回 Concurrency Utilitiesのアップデート その3](http://itpro.nikkeibp.co.jp/atcl/column/14/224071/012900016/)

