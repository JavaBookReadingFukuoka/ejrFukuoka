# 項目38 パラメータの正当性を検査する

publicのメソッドはパラメータを検査して制約が守られない場合に例外を返す。

参考：java.lang.Stringのsubstringメソッド

```Java
    /**
     * Returns a string that is a substring of this string. The
     * substring begins at the specified {@code beginIndex} and
     * extends to the character at index {@code endIndex - 1}.
     * Thus the length of the substring is {@code endIndex-beginIndex}.
     * <p>
     * Examples:
     * <blockquote><pre>
     * "hamburger".substring(4, 8) returns "urge"
     * "smiles".substring(1, 5) returns "mile"
     * </pre></blockquote>
     *
     * @param      beginIndex   the beginning index, inclusive.
     * @param      endIndex     the ending index, exclusive.
     * @return     the specified substring.
     * @exception  IndexOutOfBoundsException  if the
     *             {@code beginIndex} is negative, or
     *             {@code endIndex} is larger than the length of
     *             this {@code String} object, or
     *             {@code beginIndex} is larger than
     *             {@code endIndex}.
     */
    public String substring(int beginIndex, int endIndex) {
        if (beginIndex < 0) {
            throw new StringIndexOutOfBoundsException(beginIndex);
        }
        if (endIndex > value.length) {
            throw new StringIndexOutOfBoundsException(endIndex);
        }
        int subLen = endIndex - beginIndex;
        if (subLen < 0) {
            throw new StringIndexOutOfBoundsException(subLen);
        }
        return ((beginIndex == 0) && (endIndex == value.length)) ? this
                : new String(value, beginIndex, subLen);
    }
```
private の場合はアサーションを使う。

```Java
private static void sort(long a[], int offset, int lengh) {
    assert a != null;
    assert offset >= 0 && offset <= a.length;
    assert length >= 0 && length <= a.length - offset;
    ... // 計算を行う
}
```

アサーションは-eaオプション未指定時にコストが発生しない事が重要との意見(輪読会にて)。

コンストラクタではパラメータの検査は非常に重要。

## この規則の例外

正当性検査のコストが高いか現実的でなく、かつ、正当性検査が計算の中で暗黙に行われる場合。

Collection.sort(List)など。

## 注意事項

パラメータに制約を仮すのは良い事ではなく、妥当な処理が出来るならば制限が少ない程よいので誤解しない事。

