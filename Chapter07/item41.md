# 項目41 オーバーロードを注意して使用する

## 「オーバーロードされたどのメソッドが呼び出されるかの選択はコンパイル時に行われる」

```Java
import java.math.BigInteger;
import java.util.*;

public class CollectionClassifier {
    public static String classify(Set<?> s) {
        return "Set";
    }
    public static String classify(List<?> s) {
        return "List";
    }
    public static String classify(Collection<?> s) {
        return "Unknown Collection";
    }
    public static void main(String ... args) {
        Collection<?>[] collections = {
            new HashSet<String>(),
            new ArrayList<BigInteger>(),
            new HashMap<String, String>().values()
        };
        for (Collection<?> c : collections)
            System.out.println(classify(c));
        System.out.println(classify((HashSet<String>)collections[0])); // これなら”Set”
        System.out.println(classify((ArrayList<BigInteger>)collections[1])); // これなら”List”
    }
}
```

上記は"Unknown Collection"が３回出力される。


## 一方、オーバーライドされたメソッドは実行時に判定される

```Java
class Foodラーメン {
    String name() { return "ラーメン"; }
}
class Food豚骨ラーメン extends Foodラーメン {
    @Override String name() { return "豚骨ラーメン"; }
}
class Food長浜ラーメン extends Food豚骨ラーメン {
    @Override String name() { return "長浜ラーメン"; }
}
public class Overriding {
    public static void main(String[] args) {
        Foodラーメン[] ramens = {
            new Foodラーメン(), new Food豚骨ラーメン(), new Food長浜ラーメン()
        };
        for (Foodラーメン ramen : ramens)
            System.out.println(ramen.name());
    }
}
```

上記の実行結果は"ラーメン"、"豚骨ラーメン"、"長浜ラーメン"となる。

### 困惑させるようなオーバーロードの使用は避けるべき

### 同じパラメータ数の２つのオーバーロードされたメソッドが無ければまだ安心

```Java
void hogehoge(int first, int second);
void hogehoge(long first, long second);
void hogehoge(String first, String second);
```

こういうのを避けよう

## オートボクシングによる悲劇

```Java
import java.util.*;

public class SetList {
    public static void main(String... args) {
        Set<Integer> set = new TreeSet<>();
        List<Integer> list = new ArrayList<>();
        for (int i = -3; i < 3; i++) {
            set.add(i);
            list.add(i);
        }
        for (int i = 0; i < 3; i++) {
            set.remove(i); //0, 1, 2をremoveする
            list.remove(i); //位置が0, 1, 2をremoveする
        }
        System.out.println(set + " " + list);
    }
}
```

上記の結果は、[-3, -2, -1] [-2, 0, 2]を表示する。

List<E>インタフェースが、remove(E)とremove(int)を持つため。

## まとめ
タイトルの通り、メソッドをオーバーロードできるからと言って、安易にオーバーロードしてはダメ。

オーバーロードする時は注意して行う事。
特に、既存のクラスに新たなインタフェースを実装する場合は、同じパラメータが渡されたときに振る舞いが変わらないように注意する事。
