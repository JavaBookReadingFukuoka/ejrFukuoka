# 項目7 ファイナライザを避ける
## ファイナライザは予想不可能で危険
C++のデストラクタとは違い、一般のプログラマはほぼ使う必要はない。不要なオブジェクトはGCが回収してくれる。
メモリ以外の資源回収ならtry-finallyを使う。

## ファイナライザの欠点
即座に実行される保証がない。時間的に制約のあることはファイナライザで行ってはいけない。(ファイルクローズ等)
ファイナライザの即時性はGCのアルゴリズム依存、テスト環境と本番で全く違うこともあるため予想が難しい。
ファイナライザが実行されずにプログラムが終了する可能性もある。
パフォーマンスペナルティが大きい。

## ファイナライザのかわり
明示的終了メソッドを用意し、不要になった時点でメソッドを呼び出す。
状態を記憶し、終了後に呼び出された場合はIllegaltateExceptionをすろーする。

### 例
* InputStream、OutputStream closメソッド
* java.util.Timer cancelメソッド

明示的終了メソッドは大抵try-finallyと一緒に使用する。

## ファイナライザが有効な時
1. 明示的終了メソッドの呼び出しを忘れた時の安全ネット
上記の例のクラスのファイナライザにも実装されている。
この場合、バグであることを示すために警告を記録すべき！

2. ネイティブピアを持つオブジェクト
？？？

## ファイナライザは自動的に連鎖しない
ファイナライザが実装されたクラスを継承したサブクラスがファイナライザをオーバーライドしているときは、スーパークラスのファイナライザを呼びださなければならない。

## ファイナライザガーディアンを使用する
？？？