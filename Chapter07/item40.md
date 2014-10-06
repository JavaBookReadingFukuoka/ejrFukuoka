# 項目40 メソッドのシグネチャを注意深く設計する


### メソッド名を注意深く選ぶ事
標準命名規約に従い、オレオレルールやその場の勢いを避けろ？

### 便利なメソッドを提供しすぎたりしない事
多機能なメソッドを提供するのでなく、組み合わせ可能な単機能のメソッドを複数提供？

### 長いパラメータのリストは避ける事
４個以下を目標に

##### 同じ型のパラメータが何個も続くのは特に有害
```Java
void hogehoge(String str, String part, int start, int length);
```
とか？

##### 過度に長いパラメータのリストを短くするには、メソッドを分割するか、ヘルパークラスを使用する
java.util.Listインタフェースの例


### パラメータ型に関しては、クラスよりインタフェースを選ぶ
```Java
void hogehoge(HashMap map);
```
でなく
```Java
void hogehoge(Map map);
```
とする

### booleanパラメータより2つの要素を持つenum型を使用する
```Java
public enum TemperatureScale {
  FAHRENHEIT,
  CELSIUS
}

//可読性の比較
Thermometer.newInstance(true);
Thermometer.newInstance(TemperatureScale.CELSIUS);
```
