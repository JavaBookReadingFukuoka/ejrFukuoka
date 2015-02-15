# 71 遅延初期化を注意して使用する

## 遅延初期化
[Wikipedia](http://ja.wikipedia.org/wiki/遅延読み込み#.E9.81.85.E5.BB.B6.E5.88.9D.E6.9C.9F.E5.8C.96)
>遅延初期化（英語: lazy initialization）とは、オブジェクトや値_そしてその他の初期化の負荷が高いものの生成を後から行う戦略のことである。
>これは初期化完了を示すフラグを持ち、オブジェクトが呼び出されるごとにこのフラグを調べる。初期化が完了されていれば実際のオブジェクトを返す。初期化がまだ行われていない場合はその時点で初期化を行う。マルチスレッドのプログラムでは競合状態を防ぐために初期化完了のフラグは排他制御を行う必要がある。
>重い命令型言語では、状態の共有(shared state)を当てにしているプログラミング習慣としてこのパターンは隠れた危険性をもたらす。

初期化処理そのものが重い上、そのオブジェクトが実際に使われない場合が多いケースではたしかに遅延初期化は有用。<br>
ただ、そう思えたケースでも実際に遅延初期化無しの場合のパフォーマンスも測定するべき。

ほとんどの状況では、遅延初期化よりは普通の初期化が望ましい！！

### インスタンスフィールドの普通の初期化
```
private final FieldType field = computeFieldValue();
```

### インスタンスフィールドの遅延初期化 - 同期されたアクセッサー
```
private final FieldType field;
synchronized FieldType getField() {
    if (field == null)
        field = computeFieldValue();
    return field;
}
```

### staticフィールドの遅延初期化ホルダークラスイディオム
```
private class FieldHolder {
    static final FieldType field = computeFieldValue();
}
static FieldType getField() {
    return FieldHolder.field;
}
```
getFieldが初めて呼び出されたときに、FieldHolder.fieldが読み出されて初期化される。

### インスタンスフィールドの遅延初期化のための二重チェックイディオム
```
private volatile FieldType field;
FieldType getField() {
    FieldType result = field;
    if (result == null) { //１回目の検査ではロックしない
        synchronized(this) {
            result = field;
            if (result == null) //２回目の検査はロックあり
                result = computeFieldValue();
        }
    }
    return result;
}
```
上記は、初期化済みの場合にfieldが一度しか読み込まれないことを保証するので速い。

### 単一チェックイディオム
```
private volatile FieldType field;
FieldType getField() {
    FieldType result = field;
    if (result == null)
        result = computeFieldValue();
    return result;
}
```
初期化処理を繰り返し実行しても問題にならない場合は上記で良い。

### きわどい単一チェックイディオム
```
private int field;
int getField() {
    int result = field;
    if (result == 0)
        result = computeFieldValue();
    return result;
}
```
long、double以外のプリミティブ型は上記で良い場合がある。

## あらためてまとめ
+ ほとんどのフィールドを遅延してではなく、普通に初期化すべき。
+ やむを得ず遅延初期化する場合、適切な遅延初期化技法を使用すること。
