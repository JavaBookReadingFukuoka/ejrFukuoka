項目78 シリアライズされたインスタンスの代わりに、シリアライズ・プロキシを検討する
=================================================================================

シリアライズの問題
------------------
Serializableを実装するとバグやセキュリティ問題が起きやすい
(参考：項目74)

コンストラクタでなく言語外の機構でインスタンスを生成するから  
⇒対策 *シリアライズ・プロキシ・パターン*

シリアライズ・プロキシ・パターンの作り方
----------------------------------------

1. シリアライズ可能なクラスの private static のネストしたクラス作成
2. ネストクラスはエンクロージングクラスのインスタンスの論理状態を表現
3. ネストクラスがシリアライズ・プロキシ
4. ネストクラスはパラメータ型がエンクロージングクラスの単一コンストラクタ
5. ↑のコンストラクタで論理的状態を表現するためにデータコピーする
6. エンクロージング、プロキシともにSerializable実装宣言する
7. エンクロージングクラスは*シリアライズ時*にプロキシクラスを渡す
8. プロキシ経由でないディシリアライズを防ぐ

```java
package com.example.effective.java.c11.item77;

import java.io.InvalidObjectException;
import java.io.ObjectInputStream;
import java.io.Serializable;
import java.util.Date;

// 6.
public class Dora implements Serializable {

    private final Date useDate;

    public Dora(Date useDate) {
        this.useDate = useDate;
    }

    // 7.
    private Object writeReplace() {
        return new DoraSeriProxy(this);
    }

    // 8.
    private void readObject(ObjectInputStream stream)
            throws InvalidObjectException{
        throw new InvalidObjectException("Proxy required");
    }

    // 1. 3. 6.
    private static class DoraSeriProxy implements Serializable {
        private static final long serialVarionUID = 1L;

        // 2. 5.
        private final Date useDate;

        // 4.
        DoraSeriProxy(Dora dora) {
            // 5.
            useDate = dora.useDate;
        }

        // 9.
        private Object readResolve(){
            // 10.
            return new Dora(useDate);
        }
    }

}
```

9. シリアライズ・プロキシが*ディシリアライズ時*に本来のインスタンスを返す
10. 本来のインスタンス生成時にコンストラクタが使えて嬉しいYO


EnumSetの場合
--------------

EnumSetのstaticファクトリーメソッドは下記２パターン返す

* 64個以下:RegularEnumSet
* 64個超える:JumboEnumSet

こんな場合でもシリアライズ・プロキシなら大丈夫
1. 60個のenumを持つRegularEnumSetシリアライズした
2. シリアライズした内容に5個要素を足した
3. ディシリアライズしたらJumboEnumSetになってほしい


```java
private static class SerializationProxy<E extends Enum<E>>
        implements java.io.Serializable {
    /**
     * The element type of this enum set.
     *
     * @serial
     */
    private final Class<E> elementType;

    /**
     * The elements contained in this enum set.
     *
     * @serial
     */
    private final Enum[] elements;

    SerializationProxy(EnumSet<E> set) {
        elementType = set.elementType;
        elements = set.toArray(ZERO_LENGTH_ENUM_ARRAY);
    }

    private Object readResolve() {
        EnumSet<E> result = EnumSet.noneOf(elementType);
        for (Enum e : elements)
            result.add((E) e);
        return result;
    }

    private static final long serialVersionUID = 362491234563181265L;
}
```

制約
----

- クライアントによる拡張可能なクラス(項目17)と互換性がない
- オブジェクトグラフが循環を含んでいるようなクラスによっては互換性ない

まとめ
------

- クライアントによる拡張可能でないクラスにreadObjectやwriteObjectメソッドを
書く必要がある場合はシリアライズ・プロキシがいいよ

- シリアライズ・プロキシを使うと不変式を持つオブジェクトを
確実にシリアライズできるよ

- ちょっと遅くなるけどね
