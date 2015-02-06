# 項目3 privateのコンストラクタかenum型でシングルトン特性を強制する

シングルトンは一度しかインスタンスが作成されないクラス
注：シングルトンにするとテストが難しくなる

## シングルトンを実装する方法
* 一つ目

```java
 public class Elvis {
   public static final Elvis INSTANCE = new Elvis();
   private Elvis() {  /* コンストラクタで行う処理 */ }
   
   public void leaveTheBuilding() { /* 何か処理する */ }
 }
 ```

この方法だとリフレクションでコンストラクタが不正に呼び出される可能性がある。

 * 二つ目

```java
 public class Elvis {
   private static final Elvis INSTANCE = new Elvis();
   private Elvis() {  /* コンストラクタで行う処理 */ }
   public static Elvis getInstance() { return INSTANCE; }
   
   public void leaveTheBuilding() { /* 何か処理する */ }
 }
 ```

この方法もリフレクションで呼び出される点は変わらないが、一つ目より柔軟性がある。

* 三つ目

```java
 public enum Elvis {
   INSTANCE;
   
   public void leaveTheBuilding() { /* 何か処理する */ }
 }
 ```

機能的にはpublicにしたもの(一つ目)と変りないが、より簡潔に書くことができる。
また、シリアライズ機能をSerializableを実装しなくても提供している。

3つ目の方法はあまり知られていないが一番望ましい。