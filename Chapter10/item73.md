# 73 スレッドグループを避ける

スレッドグループは有用な機能を何も提供していないし、提供している機能の殆どに欠陥があるから使うな！！

## 一応、何のためにあったのか？ 代替は？

スレッドの基本操作を一度に多くのスレッドに適用することが出来る・・・＞その基本操作自体がNGになっているものが多い！

スレッドがthrowした例外がcatchされない場合に制御を行う方法を提供していたが？・・・＞Java5以降は[ThreadのsetUncaughtExceptionHandler](http://docs.oracle.com/javase/jp/8/api/java/lang/Thread.html#setUncaughtExceptionHandler-java.lang.Thread.UncaughtExceptionHandler-)を使うべし。
