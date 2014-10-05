* 項目39 必要な場合には防御的にコピーする

APIを使用するプログラマが間違いを犯す可能性があるので、必要があればオブジェクトの内部状態を破壊されないように「防御的なコピー」を行う事。

java.util.Dateが可変である事から攻撃可能である例



** 注意事項
防御的コピー目的でcloneメソッドを使用してはいけない

防御的コピーはパフォーマンス上のペナルティを持つ可能性があるため、常に正当化される訳ではない

防御的コピーをしない場合はドキュメンテーションに示す事

** 補足事項
今回の例のようなケースでは、Date.getTime()で返されるlongを保持するのが基本

Java8での新しいDateTimeAPIでは以下のクラス等？は不変クラス
- java.time.LocalDate
- java.time.LocalDateTime
- java.time.LocalTime
- java.time.OffsetDateTime
- java.time.OffsetTime
- java.time.ZonedDateTime

