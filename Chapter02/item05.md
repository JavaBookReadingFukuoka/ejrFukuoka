# ����5 �s�K�v�ȃI�u�W�F�N�g�̐����������
## �I�u�W�F�N�g���s�ςł���΍ė��p�ł���
### ������

```java
String s = new String("hogehoge");
```

### ���R
�@�\�I�ɓ����I�u�W�F�N�g���K�v�Ȏ��A�V��������������g���܂킵���ق����ǂ��B
�I�u�W�F�N�g���s�ςł���΍ė��p���ł���B
���̕��͎��s���ɐV���ɃC���X�^���X�𐶐�����B"hogehoge"�͂��ꎩ�g��String�C���X�^���X�Ȃ̂�new String����K�v�͂Ȃ��B
���̕������[�v���ɂ��������Ȃǂ̓C���X�^���X���������[�v�̉񐔂����s����B

### ���P

```java
String s = "hogehoge";
```

���̃R�[�h�͂ЂƂ�String�C���X�^���X���g���܂킷�B
�܂����������񃊃e�����Ȃ瓯�ꉼ�z�}�V���̑��R�[�h�ł��I�u�W�F�N�g���g���܂킳��邱�Ƃ��ۏ؂����B

## static�t�@�N�g���[���\�b�h�̎g�p
static�t�@�N�g���[���\�b�h(����1)�ƃR���X�g���N�^�𗼕��񋟂��Ă���s�σN���X�́Astatic�t�@�N�g���[���\�b�h�̕����g�p���邱�Ƃŏd�������I�u�W�F�N�g�̐���������ł���B

## ��

```java
Boolean.valueOf(String)
Boolean(String)

```

����2�ł͑O�҂̂ق����]�܂����B�R���X�g���N�^�͌Ăяo����閈�ɐV���ɃI�u�W�F�N�g�𐶐�����B

## �σI�u�W�F�N�g(immutable object)�̍ė��p
### ������
```java
public class Person {
/* �ȗ� */

  //����Ă͂����Ȃ�
  public boolean isYutori() {
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1987, Calendar.APRIL, 1, 0, 0, 0);
    Date yutoriStart = gmtCal.getTime();
    gmtCal.set(2004, Calendar.APRIL, 1, 0, 0, 0);
    Date yutoriEnd = gmtCal.getTime();
    
    return birthDate.compareTo(yotoriStart) >= 0 && birthDate.compareTo(yotoriEnd) <0;
  }
}
```

### ���R
���̃��\�b�h�͌Ăяo����閈��Calendar�C���X�^���X��Date�C���X�^���X�𐶐�����B

### ���P
```java
public class Person {
/* �ȗ� */

  private static final Date YUTORI_START;
  private static final Date YUTORI_END
  
  static {
    Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
    gmtCal.set(1987, Calendar.APRIL, 1, 0, 0, 0);
    YUTORI_START = gmtCal.getTime();
    gmtCal.set(2004, Calendar.APRIL, 1, 0, 0, 0);
    YUTORI_END = gmtCal.getTime();
  }

  public boolean isYutori() {
    return birthDate.compareTo(YUTORI_START) >= 0 && birthDate.compareTo(YUTORI_END) <0;
  }
}
```

YUTORI_START�AYUTORI_END�̓N���X���������Ɉ�x�������������B�܂������I�ȍ~���Ƃ��Ē萔�ł��邱�Ƃ������ɂȂ�B
���\�b�h���g��Ȃ������C���X�^���X����������Ă��܂��B����͒x��������(����71)�ŉ�����邱�Ƃ͂ł��邪���������G�ɂȂ銄�ɍ~���͑傫���Ȃ��̂ł������߂ł��Ȃ��B

## �ė��p�ł���̂������łȂ��ꍇ(�A�_�v�^�[�p�^�[���ɂ���)
### ��
Map�C���^�[�t�F�[�X��KeySet���\�b�h�͓���Set�I�u�W�F�N�g��Ԃ��Ă��A�Ԃ��ꂽ�I�u�W�F�N�g�͋@�\�I�ɓ����ׁA����ύX���ꂽ�瑼�̃I�u�W�F�N�g���ׂĂ��ύX�����B
(����Map�C���X�^���X�Ƀo�b�N�A�b�v����Ă��邽�߁H)

## �I�[�g�{�N�V���O�ɒ���
J2SE1.5�ȍ~�ł̓v���~�e�B�u�^�ƃ{�N�V���O���ꂽ�I�u�G�N�Ƃ̈Ⴂ���s����

long��Long��typo���邾���Ŏ����Ń{�N�V���O����Ӑ}�����ɃI�u�W�F�N�g�����������B
�{�N�V���O���ꂽ��{�f�[�^�^����{�f�[�^�^��I�ԁB�Ӑ}���Ȃ��I�[�g�{�N�V���O�ɒ���

## �I�u�W�F�N�g�����̓R�X�g���|���������ׂ����Ƃ����킯�ł͂Ȃ�
�ŐV��JVM�ł͏����ɃI�u�W�F�N�g�𐶐��E������邱�ƂɃR�X�g�͊|����Ȃ��B���Đ���Ȍ����̂��߂ɂ͈�ʓI�ɗǂ�����
�I�u�W�F�N�g�v�[���ŊǗ�����̂�DB�R�l�N�V�������������d�����̂̂݁A��ʓI�ɂ̓I�u�W�F�N�g�v�[���͗��G�ɂȂ邽�ߔ�����B
