# 60. 標準例外を使用する

オレオレExceptionを無闇に定義することなかれ

## Java標準のExceptionを積極的に使うメリット

+ 学習コストが少なくて済む
+ 見慣れない例外でごちゃごちゃしないので、APIを使用するコードも読みやすくなる

## よく使われる例外

+ IllegalArgumetnException - メソッド呼び出しのパラメタが不適切
+ IllegalStateException - メソッド呼び出し前のオブジェクトの状態が不正
+ NullPointerException - パラメタにnullが禁止されているのに渡してきやがった
+ IndexOutOfBoudsException - インデックスパラメタが範囲外
+ ConcurrentModificationException - マルチスレッドでの使用が禁止されてるのに呼び出された
+ UnsupportedOperationException - オブジェクトがメソッドをサポートしていない。例えば、追加のみ可能なListで要素を削除しようとしたとか
