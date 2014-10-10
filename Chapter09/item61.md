# 61. 抽象概念に適した例外をスローする

上位レイヤは甲斐レベルの例外をキャッチして、上位レイヤの中で上位レベルの抽象概念の観点んから説明可能な例外をスローすべき(例外翻訳)

```
try {
    ...
} catch (LowerLevelException e) {
    throw new HigherLevelException(...);
}
```

+ API使用者に混乱を与えないための施策
+ 例外を発生させた原因をデバッグするために、例外連鎖(exception chaining)をするのが適切

```
try {
    ...
} catch (LowerLevelException cause) {
    throw new HigherLevelException(cause);
}

...

class HigherLevelException extends Exception {
    HigherLevelException(Throwable cause) {
        super(cause);
    }
}
```

+ ただし、濫用は禁物。可能であればそもそも下位レベルの例外が発生しないよう努めるべき

## 例外翻訳を使ってもいい条件

### 例外発生を防げない
+ 下位レイヤからの例外を防いだり、下位レイヤの例外から上位レイヤを隔離することが実現不能

### 例外翻訳しないと使用者が混乱する
+ 下位レベルのメソッドがフローするどれかの例外が、上位レイヤに対して不適切
