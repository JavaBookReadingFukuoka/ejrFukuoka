# 57. 例外的状態にだけ例外を使用する

## 悪い例

```
try {
    int i = 0;
    while (true) {
        range[i++].climb();
    }
} catch(ArrayIndexOutOfBoundsException e) {
    // 正常
}
```

## 悪い理由

+ VMにとって例外処理は"重い"
+ try-catchブロック内の処理は最適化され難い事がある
+ 配列を順次処理するイディオムはVMに最適化されるのに対して、配列を例外で処理するイディオムは最適化されない
