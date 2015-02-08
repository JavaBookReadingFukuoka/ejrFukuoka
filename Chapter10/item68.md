# 68 スレッドよりエグゼキューターとタスクを選ぶ

## エグゼキューターフレームワーク
[java.util.concurrent](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/package-summary.html)が、Java5.0で追加された。<br>
並行プログラミングでよく使用されるユーティリティ・クラス。

```
//例
//ワークキューを作成
ExecuterService executor = Executors.newSingleThreadExecutor();
//runnableを実行
executor.execute(runnable);
//エグゼキューターに終了を指示
executor.shutdown();
```
+ [ExecutorService](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ExecutorService.html) : 終了を管理するメソッド、および1つ以上の非同期タスクの進行状況を追跡するFutureを生成できるメソッドを提供するExecutorです。
+ [Executors](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Executors.html) : このパッケージで定義された[Executor](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Executor.html)、[ExecutorService](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ExecutorService.html)、[ScheduledExecutorService](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ScheduledExecutorService.html)、[ThreadFactory](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ThreadFactory.html)、および[Callable](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Callable.html)クラス用のファクトリおよびユーティリティ・メソッドです。このクラスは、次の種類のメソッドをサポートします。

エグゼーキューターフレームワークを使うと自分で直接スレッドを使うよりもいろいろ嬉しい模様。

+ 特定のタスクが完了するのを待つことが出来る
+ どれかのタスクが完了するのを待つことが出来る（[invokeAnyメソッド](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ExecutorService.html#invokeAny-java.util.Collection-)を使用）
+ すべてのタスクが完了するのを待つことが出来る（[invokeAllメソッド](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ExecutorService.html#invokeAll-java.util.Collection-)を使用）
+ エグゼキューターサービスがきちんと完了するのを待つことが出来る（[awaitTerminationメソッド](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ExecutorService.html#awaitTermination-long-java.util.concurrent.TimeUnit-)を使用）
+ タスクが完了するごとに１つずつのタスクの結果を取り出すことが出来る（[ExecutorCompletionService](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ExecutorCompletionService.html)を使用）

## スレッドプール（thread pool）
キューからのリクエストを処理するスレッドが２つ以上欲しい場合使う？

+ [ExecutorService.newCachedThreadPool()](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Executors.html#newCachedThreadPool--) : 必要に応じ、新規スレッドを作成するスレッド・プールを作成しますが、利用可能な場合には以前に構築されたスレッドを再利用します。
+ [ExecutorService.newFixedThreadPool()](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Executors.html#newFixedThreadPool-int-) : 共有アンバウンド形式のキューなしで動作する、固定数のスレッドを再利用するスレッド・プールを作成します。
+ [ExecutorService.newScheduledThreadPool()](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Executors.html#newScheduledThreadPool-int-) : 指定された遅延時間後、または周期的にコマンドの実行をスケジュールできる、スレッド・プールを作成します。

小さなプログラムや軽い負荷のサーバーにはキャッシュされたスレッドプールは良い。<br>
高負荷の製品サーバーでは固定数のスレッドプールを使うか、最大限の制御をするために[ThreadPoolExecutor](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ThreadPoolExecutor.html)を利用するべき。

## タスク
[java.lang.Thread](http://docs.oracle.com/javase/jp/8/api/java/lang/Thread.html)を避け、[java.lang.Runnable](http://docs.oracle.com/javase/jp/8/api/java/lang/Runnable.html)か[java.util.concurrent.Callable](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/Callable.html)を使う

RunnableとCallableは関数型インタフェースなので、Java8ではラムダ式またはメソッド参照の代入先として使用できます。

## タイマー処理
java.util.Timerの代わりに、[ScheduledExecutorService](http://docs.oracle.com/javase/jp/8/api/java/util/concurrent/ScheduledExecutorService.html)が使える。<br>
精度が重要な場合や、例外からの回復を考慮するならばこちらの方が良い。

