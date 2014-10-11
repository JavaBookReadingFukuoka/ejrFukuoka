# 項目42 可変長引数を注意して使用する

1つ以上の引数を渡す場合、などは注意してメソッドを設計する必要がある。

Java1.4以前のプログラムで広く使われていたイディオムがJava5以降で悲しい事になった。

```Java
public class ArrayToString {
    public static void main(String ... args) {
        //基本型配列
        int[] digits = {3, 1, 4, 1, 5, 9, 2, 6, 5, 4};
        System.out.println(java.util.Arrays.asList(digits));//JDK1.4でコンパイルエラー
        System.out.println(java.util.Arrays.toString(digits));//JDK1.5以降のAPI
        //参照型配列
        Integer[] digits2 = {3, 1, 4, 1, 5, 9, 2, 6, 5, 4};//JDK1.4では手動ボクシング必要
        System.out.println(java.util.Arrays.asList(digits2));
        System.out.println(java.util.Arrays.toString(digits2));//JDK1.5以降のAPI
    }
}
```
上記を実行すると（Java5.0以降）、以下のように表示される。
```
[[I@7f31245a]
[3, 1, 4, 1, 5, 9, 2, 6, 5, 4]
[3, 1, 4, 1, 5, 9, 2, 6, 5, 4]
[3, 1, 4, 1, 5, 9, 2, 6, 5, 4]
```
（…でももはやJava9でjava1.4サポートされなくなるらしいし、見捨てて良く無い？）

## 可変長引数を持つメソッドは以下のように作る策もある

```
public void foo() {}
public void foo(int a1) {}
public void foo(int a1, int a2) {}
public void foo(int a1, int a2, int a3) {}
public void foo(int a1, int a2, int a3, int ... rest) {}
```
