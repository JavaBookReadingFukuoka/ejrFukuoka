# 項目39 必要な場合には防御的にコピーする

APIを使用するプログラマが間違いを犯す可能性があるので、必要があればオブジェクトの内部状態を破壊されないように「防御的なコピー」を行う事。

java.util.Dateが可変である事から攻撃可能である例

```Java
import java.text.*;
import java.util.*;

public class PeriodSample1 {
    private DateFormat df = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss SSS");
    public static void main(String ... args) {
        (new PeriodSample1()).mainLogic();
    }
    public void mainLogic() {
        Date start = new Date();
        Date end = new Date();
        Period p = new Period(start, end);
        System.out.println(p.toString());
        end.setYear(78);
        System.out.println(p.toString());
    }
    private final class Period {
        private final Date start;
        private final Date end;
        public Period(Date start, Date end) {
            if (start.compareTo(end) > 0)
                throw new IllegalArgumentException(start + " after " + end);
            this.start = start;
            this.end = end;
        }
        public Date start() {
            return start;
        }
        public Date end() {
            return end;
        }
        public String toString() {
            return "start=[" + df.format(start) + "] end=[" + df.format(end) + "]";
        }
    }
}
```

```Java
import java.text.*;
import java.util.*;

public class PeriodSample2 {
    private DateFormat df = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss SSS");
    public static void main(String ... args) {
        (new PeriodSample2()).mainLogic();
    }
    public void mainLogic() {
        Date start = new Date();
        Date end = new Date();
        Period p = new Period(start, end);
        System.out.println(p.toString());
        end.setYear(78);
        System.out.println(p.toString());
        p.end().setYear(78);
        System.out.println(p.toString());
    }
    private final class Period {
        private final Date start;
        private final Date end;
        public Period(Date start, Date end) {
            if (start.compareTo(end) > 0)
                throw new IllegalArgumentException(start + " after " + end);
            this.start = new Date(start.getTime());
            this.end = new Date(end.getTime());
        }
        public Date start() {
            return start;
        }
        public Date end() {
            return end;
        }
        public String toString() {
            return "start=[" + df.format(start) + "] end=[" + df.format(end) + "]";
        }
    }
}
```

防御的コピーの実施

```Java
import java.text.*;
import java.util.*;

public class PeriodSample3 {
    private DateFormat df = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss SSS");
    public static void main(String ... args) {
        (new PeriodSample3()).mainLogic();
    }
    public void mainLogic() {
        Date start = new Date();
        Date end = new Date();
        Period p = new Period(start, end);
        System.out.println(p.toString());
        end.setYear(78);
        System.out.println(p.toString());
        p.end().setYear(78);
        System.out.println(p.toString());
    }
    private final class Period {
        private final Date start;
        private final Date end;
        public Period(Date start, Date end) {
            if (start.compareTo(end) > 0)
                throw new IllegalArgumentException(start + " after " + end);
            this.start = new Date(start.getTime());
            this.end = new Date(end.getTime());
        }
        public Date start() {
            return new Date(start.getTime());
        }
        public Date end() {
            return new Date(end.getTime());
        }
        public String toString() {
            return "start=[" + df.format(start) + "] end=[" + df.format(end) + "]";
        }
    }
}
```

## 注意事項

防御的コピー目的でcloneメソッドを使用してはいけない

防御的コピーはパフォーマンス上のペナルティを持つ可能性があるため、常に正当化される訳ではない

防御的コピーをしない場合はドキュメンテーションに示す事

## 補足事項

今回の例のようなケースでは、Date.getTime()で返されるlongを保持するのが基本

```Java
import java.text.*;
import java.util.*;

public class PeriodSample4 {
    private DateFormat df = new SimpleDateFormat("yyyy/MM/dd HH:mm:ss SSS");
    public static void main(String ... args) {
        (new PeriodSample4()).mainLogic();
    }
    public void mainLogic() {
        Date start = new Date();
        Date end = new Date();
        Period p = new Period(start, end);
        System.out.println(p.toString());
        end.setYear(78);
        System.out.println(p.toString());
        p.end().setYear(78);
        System.out.println(p.toString());
    }
    private final class Period {
        private final long start;
        private final long end;
        public Period(Date start, Date end) {
            if (start.compareTo(end) > 0)
                throw new IllegalArgumentException(start + " after " + end);
            this.start = start.getTime();
            this.end = end.getTime();
        }
        public Date start() {
            return new Date(start);
        }
        public Date end() {
            return new Date(end);
        }
        public String toString() {
            return "start=[" + df.format(new Date(start)) + "] end=[" + df.format(new Date(end)) + "]";
        }
    }
}
```

Java8での新しいDateTimeAPIでは以下のクラス等？は不変クラス
- java.time.LocalDate
- java.time.LocalDateTime
- java.time.LocalTime
- java.time.OffsetDateTime
- java.time.OffsetTime
- java.time.ZonedDateTime

