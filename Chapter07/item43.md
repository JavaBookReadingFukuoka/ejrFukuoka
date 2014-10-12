# 項目43 nullではなく、空配列か空コレクションを渡す

nullを返してしまうとAPI呼び出し側で余分な処理が必要になるので、空配列や空コレクションを返せ！！

## 配列でnullを返してしまう

```Java
public class ArrayReturnNull {
    private static class Wrong {
        private final String[] wrongArray = {};
        public String[] returnWrongArray() {
            if (wrongArray.length == 0) {
                return null;
            }
            return wrongArray;
        }
    }
    public static void main(String... args) {
        Wrong wrong = new Wrong();
        String[] wrongArray = wrong.returnWrongArray();
        System.out.println(wrongArray.length); //NullPointerException
    }
}
```

## コレクションでnullを返してしまう

```Java
import java.util.*;

public class ListReturnNull {
    private static class Wrong {
        private final List<String> wrongList = new ArrayList<>();
        public List<String> returnWrongList() {
            if (wrongList.isEmpty()) {
                return null;
            }
            return wrongList;
        }
    }
    public static void main(String... args) {
        Wrong wrong = new Wrong();
        List<String> wrongList = wrong.returnWrongList();
        System.out.println(wrongList.size()); //NullPointerException
    }
}
```

上記２つはnullを返してしまったがためにNullPointerExceptionががが

## 空配列を返そう

```Java
public class ArrayReturnEmpty {
    private static class Wrong {
        private final String[] wrongArray = {};
        public String[] returnWrongArray() {
            if (wrongArray.length == 0) {
                return new String[0];
            }
            return wrongArray;
        }
    }
    public static void main(String... args) {
        Wrong wrong = new Wrong();
        String[] wrongArray = wrong.returnWrongArray();
        System.out.println(wrongArray.length);
    }
}
```

## 空コレクションを返そう

```Java
import java.util.*;

public class ListReturnEmpty {
    private static class Wrong {
        private final List<String> wrongList = new ArrayList<>();
        public List<String> returnWrongList() {
            if (wrongList.isEmpty()) {
                return Collections.emptyList();
            }
            return wrongList;
        }
    }
    public static void main(String... args) {
        Wrong wrong = new Wrong();
        List<String> wrongList = wrong.returnWrongList();
        System.out.println(wrongList.size());
    }
}
```

## Collections.emptyList()

Collections.emptyList()を使うと、Immutableな空Listを取得出来るそうですよ？

```Java
import java.util.*;

public class EmptyListTest {
    public static void main(String... args) {
        List<String> emptyList = Collections.emptyList();
        emptyList.add("java.lang.UnsupportedOperationException");
    }
}
```

## Collections.unmodifiableList()

Listを編集不可にしたい場合はCollections.unmodifiableList()を使うそうですよ？

```Java
import java.util.*;

public class UnmodifiableListTest {
    public static void main(String[] args) {
        List<Date> list = new ArrayList<>();
        list.add(new Date());
        list.add(new Date());
        list.add(new Date());
        list = Collections.unmodifiableList(list);
        try {
            list.add(new Date());
        } catch (java.lang.UnsupportedOperationException e) {
            System.out.println("追加出来ない：" + e.toString());
        }
        try {
            list.remove(0);
        } catch (java.lang.UnsupportedOperationException e) {
            System.out.println("削除も出来ない：" + e.toString());
        }
        list.get(0).setYear(78); // 中身は編集出来る
        list.stream().forEach((str) -> {
            System.out.println(str);
        });
    }
}
```

## 配列やコレクションでない場合はnullを返すしか無い？？

Java8では「[null書いたら負け](http://www.slideshare.net/nowokay/null-35862621)」！
[Java8でのプログラムの構造を変えるOptional](http://d.hatena.ne.jp/nowokay/20130524)


```Java
import java.util.*;

public class DateReturnOptional {
    private static Optional<Date> returnOptionalDate(int i) {
        switch (i) {
            case 0:
                return Optional.of(new Date());
            case 1:
                return Optional.empty();//空要素
            case 2:
                return Optional.of(null);//これはNullPointerException
        }
        return Optional.empty();
    }
    public static void main(String[] args) {
        for (int i = 0; i < 3; i++) {
            System.out.println("i=" + i + "のOptionalを生成・・・");
            Optional<Date> odate;
            try {
                odate = returnOptionalDate(i);
            } catch (java.lang.NullPointerException e) {
                System.out.println("ぬるぽ：" + e);
                continue;
            }
            System.out.println("i=" + i + "のOptional：" + odate);
            odate.ifPresent(d -> System.out.println("d.getTime：" + d.getTime()));
        }
    }
}
```
上記の実行結果は
```
i=0のOptionalを生成・・・
i=0のOptional：Optional[Sat Oct 11 11:36:14 JST 2014]
d.getTime：1412994974923
i=1のOptionalを生成・・・
i=1のOptional：Optional.empty
i=2のOptionalを生成・・・
ぬるぽ：java.lang.NullPointerException
```
-i=0は普通の値なので、Optionalを通常通り生成出来るし、d.getTimeも取得出来る
-i=1はOptional.emptyなので、Optionalを生成出来るが、ifPresentでは実行されない
-i=2はOptionalを生成する時点でNullPointerExceptionなのでnullを漏らす事を防げる
