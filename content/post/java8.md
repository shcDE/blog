---
title: "자바 공부 기록(8) - java.lang패키지와 유용한 클래스에 대해 알아볼까요?"
date: 2022-07-27T14:05:55+09:00
categories:
- development
tags:
- development
keywords:
- backend, java, development
image: coffee-gab9b09d77_1920.jpg
---
![java](https://github.com/shcDE/pictures/blob/main/images_for_blog/coffee-gab9b09d77_1920.jpg?raw=true)
________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 java.lang패키지와 유용한 클래스에 대해 알아보겠습니다. java.lang패키지는 자바 프로그래밍에서 가장 기본이 되는 클래스들을 포함하고 있습니다. 그동안 String 클래스나 System 클래스를 import문 없이 사용할 수 있었던 이유는 바로 java.lang패키지에 속한 클래스들이었기 때문이었습니다. 제 포스팅에서는 java.lang패키지의 여러 클래스들 중에서도 자주 사용되는 클래스들을 일부만 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 9장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
________________________________________________________________________________________________________________________________________________________________________
Object 클래스를 활용한 예시는 다음과 같이 10개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-1
```java
class EqualsEx1 {
    public static void main(String[] args) {
        Value v1 = new Value(10);
        Value v2 = new Value(10);

        if (v1.equals(v2)) {
            System.out.println("v1과 v2는 같습니다.");
        } else {
            System.out.println("v1과 v2는 다릅니다.");
        }

        v2 = v1;

        if (v1.equals(v2)) {
            System.out.println("v1과 v2는 같습니다.");
        } else {
            System.out.println("v1과 v2는 다릅니다.");
        }
    } // main
}

class Value {
    int value;

    Value(int value) {
        this.value = value;
    }
}
```
```
v1과 v2는 다릅니다.
v1과 v2는 같습니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-2
```java
class Person {
    long id;

    public boolean equals(Object obj) {
        if(obj!=null && obj instanceof Person) {
            return id ==((Person)obj).id;
        } else {
            return false;
        }
    }

    Person(long id) {
        this.id = id;
    }
}

class EqualsEx2 {
    public static void main(String[] args) {
        Person p1 = new Person(8011081111222L);
        Person p2 = new Person(8011081111222L);

        if(p1==p2)
            System.out.println("p1과 p2는 같은 사람입니다.");
        else
            System.out.println("p1과 p2는 다른 사람입니다.");

        if(p1.equals(p2))
            System.out.println("p1과 p2는 같은 사람입니다.");
        else
            System.out.println("p1과 p2는 다른 사람입니다.");

    }
}
```
```
p1과 p2는 다른 사람입니다.
p1과 p2는 같은 사람입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-3
```java
class HashCodeEx1 {
    public static void main(String[] args) {
        String str1 = new String("abc");
        String str2 = new String("abc");

        System.out.println(str1.equals(str2));
        System.out.println(str1.hashCode());
        System.out.println(str2.hashCode());
        System.out.println(System.identityHashCode(str1));
        System.out.println(System.identityHashCode(str2));
    }
}
```
```
true
96354
96354
21685669
2133927002

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-4
```java
class Card {
    String kind;
    int number;

    Card() {
        this("SPADE", 1);
    }

    Card(String kind, int number) {
        this.kind = kind;
        this.number = number;
    }
}

class CardToString {
    public static void main(String[] args) {
        Card c1 = new Card();
        Card c2 = new Card();

        System.out.println(c1.toString());
        System.out.println(c2.toString());
    }
}
```
```
Card@14ae5a5
Card@7f31245a

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-5
```java
class ToStringTest {
    public static void main(String args[]) {
        String str = new String("KOREA");
        java.util.Date today = new java.util.Date();

        System.out.println(str);
        System.out.println(str.toString());
        System.out.println(today);
        System.out.println(today.toString());
    }
}
```
```
KOREA
KOREA
Wed Jul 27 14:54:14 KST 2022
Wed Jul 27 14:54:14 KST 2022

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-6
```java
class Card {
    String kind;
    int number;

    Card() {
        this("SPADE", 1);  // Card(String kind, int number)�� ȣ��
    }

    Card(String kind, int number) {
        this.kind = kind;
        this.number = number;
    }

    public String toString() {
        // Card인스턴스의 kind와 number를 문자열로 반환한다.
        return "kind : " + kind + ", number : " + number;
    }
}

class CardToString2 {
    public static void main(String[] args) {
        Card c1 = new Card();
        Card c2 = new Card("HEART", 10);
        System.out.println(c1.toString());
        System.out.println(c2.toString());
    }
}
```
```
kind : SPADE, number : 1
kind : HEART, number : 10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-7
```java
class Point implements Cloneable {
    int x;
    int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public String toString() {
        return "x="+x +", y="+y;
    }

    public Object clone() {
        Object obj = null;
        try {
            obj = super.clone();  // clone()은 반드시 예외처리를 해주어야 한다.
        } catch(CloneNotSupportedException e) {}
        return obj;
    }
}

class CloneEx1 {
    public static void main(String[] args){
        Point original = new Point(3, 5);
        Point copy = (Point)original.clone(); // 복제(clone)해서 새로운 객체를 생성
        System.out.println(original);
        System.out.println(copy);
    }
}
```
```
x=3, y=5
x=3, y=5

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-8
```java
import java.util.*;

class CloneEx2 {
    public static void main(String[] args){
        int[] arr = {1,2,3,4,5};

        // 배열 arr을 복제해서 같은 내용의 새로운 배열을 만든다.
        int[] arrClone = arr.clone();
        arrClone[0]= 6;

        System.out.println(Arrays.toString(arr));
        System.out.println(Arrays.toString(arrClone));
    }
}
```
```
[1, 2, 3, 4, 5]
[6, 2, 3, 4, 5]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-9
```java
class Circle implements Cloneable {
    Point p;  // 원점
    double r; // 반지름

    Circle(Point p, double r) {
        this.p = p;
        this.r = r;
    }

    public Circle shallowCopy() { // 얕은 복사
        Object obj = null;

        try {
            obj = super.clone();
        } catch (CloneNotSupportedException e) {}

        return (Circle)obj;
    }

    public Circle deepCopy() { // 깊은 복사
        Object obj = null;

        try {
            obj = super.clone();
        } catch (CloneNotSupportedException e) {}

        Circle c = (Circle)obj;
        c.p = new Point(this.p.x, this.p.y);

        return c;
    }

    public String toString() {
        return "[p=" + p + ", r="+ r +"]";
    }
}

class Point {
    int x;
    int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public String toString() {
        return "("+x +", "+y+")";
    }
}

class ShallowCopy {
    public static void main(String[] args) {
        Circle c1 = new Circle(new Point(1, 1), 2.0);
        Circle c2 = c1.shallowCopy();
        Circle c3 = c1.deepCopy();

        System.out.println("c1="+c1);
        System.out.println("c2="+c2);
        System.out.println("c3="+c3);
        c1.p.x = 9;
        c1.p.y = 9;
        System.out.println("= c1의 변경 후 =");
        System.out.println("c1="+c1);
        System.out.println("c2="+c2);
        System.out.println("c3="+c3);
    }
}
```
```
c1=[p=(1, 1), r=2.0]
c2=[p=(1, 1), r=2.0]
c3=[p=(1, 1), r=2.0]
= c1의 변경 후 =
c1=[p=(9, 9), r=2.0]
c2=[p=(9, 9), r=2.0]
c3=[p=(1, 1), r=2.0]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-10
```java
final class Card {
    String kind;
    int num;

    Card() {
        this("SPADE", 1);
    }

    Card(String kind, int num) {
        this.kind = kind;
        this.num  = num;
    }

    public String toString() {
        return kind + ":" + num;
    }
}

class ClassEx1 {
    public static void main(String[] args) throws Exception {
        Card c  = new Card("HEART", 3);       // new연산자로 객체 생성
        Card c2 = Card.class.newInstance();   // Class객체를 통해서 객체 생성

        Class cObj = c.getClass();

        System.out.println(c);
        System.out.println(c2);
        System.out.println(cObj.getName());
        System.out.println(cObj.toGenericString());
        System.out.println(cObj.toString());
    }
}
```
```
HEART:3
SPADE:1
Card
final class Card
class Card

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 String 클래스에 대해 알아보겠습니다. String 클래스란 기존의 다른 언어에서 문자열을 char형의 배열로 다룬 반면, 자바에서는 문자열을 위해 제공되는 클래스입니다. 이러한 String 클래스는 문자열을 저장하고 이를 다루는데 필요한 메서드를 함께 제공합니다. String 클래스에 대한 예제는 다음과 같이 7개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-11
```java
class StringEx1 {
    public static void main(String[] args) {
        String str1 = "abc";
        String str2 = "abc";

        System.out.println("String str1 = \"abc\";");
        System.out.println("String str2 = \"abc\";");

        System.out.println("str1 == str2 ?  " + (str1 == str2));
        System.out.println("str1.equals(str2) ? " + str1.equals(str2));
        System.out.println();

        String str3 = new String("\"abc\"");
        String str4 = new String("\"abc\"");

        System.out.println("String str3 = new String(\"abc\");");
        System.out.println("String str4 = new String(\"abc\");");

        System.out.println("str3 == str4 ? " + (str3 == str4));
        System.out.println("str3.equals(str4) ? " + str3.equals(str4));
    }
}
```
```
String str1 = "abc";
String str2 = "abc";
str1 == str2 ?  true
str1.equals(str2) ? true

String str3 = new String("abc");
String str4 = new String("abc");
str3 == str4 ? false
str3.equals(str4) ? true

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-12
```java
class StringEx2 {
    public static void main(String args[]) {
        String s1 = "AAA";
        String s2 = "AAA";
        String s3 = "AAA";
        String s4 = "BBB";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
예제 9-12를 실행하면 StringEx2.jar 파일이 생성됩니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-13
```java
class StringEx3 {
    public static void main(String[] args) {
        // 길이가 0인 char배열을 생성한다.
        char[] cArr = new char[0];   // char[] cArr = {};와 같다.
        String s = new String(cArr); // String s = new String("");와 같다.

        System.out.println("cArr.length="+cArr.length);
        System.out.println("@@@"+s+"@@@");
    }
}
```
```
cArr.length=0
@@@@@@

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-14
```java
import java.util.StringJoiner;

class StringEx4 {
    public static void main(String[] args) {
        String animals = "dog,cat,bear";
        String[] arr   = animals.split(",");

        System.out.println(String.join("-", arr));

        StringJoiner sj = new StringJoiner("/","[","]");
        for(String s : arr)
            sj.add(s);

        System.out.println(sj.toString());
    }
}
```
```
dog-cat-bear
[dog/cat/bear]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-15
```java
import java.util.StringJoiner;

class StringEx5 {
    public static void main(String[] args) throws Exception {
        String str = "가";

        byte[] bArr  = str.getBytes("UTF-8");
        byte[] bArr2 = str.getBytes("CP949");

        System.out.println("UTF-8:" + joinByteArr(bArr));
        System.out.println("CP949:" + joinByteArr(bArr2));

        System.out.println("UTF-8:" + new String(bArr,  "UTF-8"));
        System.out.println("CP949:" + new String(bArr2, "CP949"));
    }

    static String joinByteArr(byte[] bArr) {
        StringJoiner sj = new StringJoiner(":", "[", "]");

        for(byte b : bArr)
            sj.add(String.format("%02X",b));

        return sj.toString();
    }
}
```
```
UTF-8:[EA:B0:80]
CP949:[B0:A1]
UTF-8:가
CP949:가

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-16
```java
class StringEx6 {
    public static void main(String[] args) {
        int iVal = 100;
        String strVal = String.valueOf(iVal);	// int를 String으로 변환한다.

        double dVal = 200.0;
        String strVal2 = dVal + "";	// int를 String으로 변환하는 또 다른 방법

        double sum  = Integer.parseInt("+"+strVal)+Double.parseDouble(strVal2);
        double sum2 = Integer.valueOf(strVal) + Double.valueOf(strVal2);

        System.out.println(String.join("",strVal,"+",strVal2,"=")+sum);
        System.out.println(strVal+"+"+strVal2+"="+sum2);
    }
}
```
```
100+200.0=300.0
100+200.0=300.0

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-17
```java
class StringEx7 {
    public static void main(String[] args) {
        String fullName = "Hello.java";

        // fullName에서 '.'의 위치를 찾는다.
        int index = fullName.indexOf('.');

        // fullName의 첫번째 글자부터 '.'이 있는 곳까지 문자열을 추출한다.
        String fileName = fullName.substring(0, index);

        // '.'의 다음 문자부터 시작해서 문자열의 끝까지 추출한다.
        // fullName.substring(index+1, fullName.length());의 결과와 같다.
        String ext = fullName.substring(index+1);

        System.out.println(fullName + "의 확장자를 제외한 이름은 " + fileName);
        System.out.println(fullName + "의 확장자는 " + ext);
    }
}
```
```
Hello.java의 확장자를 제외한 이름은 Hello
Hello.java의 확장자는 java

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
StringBuffer 클래스와 StringBuilder 클래스에 대한 예시는 다음 두개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-18
```java
class StringBufferEx1 {
    public static void main(String[] args) {
        StringBuffer sb  = new StringBuffer("abc");
        StringBuffer sb2 = new StringBuffer("abc");

        System.out.println("sb == sb2 ? " + (sb == sb2));
        System.out.println("sb.equals(sb2) ? " + sb.equals(sb2));

        // StringBuffer의 내용을 String으로 변환한다.
        String s  = sb.toString();	// String s = new String(sb);와 같다.
        String s2 = sb2.toString();

        System.out.println("s.equals(s2) ? " + s.equals(s2));
    }
}
```
```
sb == sb2 ? false
sb.equals(sb2) ? false
s.equals(s2) ? true

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-19
```java
class  StringBufferEx2 {
    public static void main(String[] args) {
        StringBuffer sb  = new StringBuffer("01");
        StringBuffer sb2 = sb.append(23);
        sb.append('4').append(56);

        StringBuffer sb3 = sb.append(78);
        sb3.append(9.0);

        System.out.println("sb ="+sb);
        System.out.println("sb2="+sb2);
        System.out.println("sb3="+sb3);

        System.out.println("sb ="+sb.deleteCharAt(10));
        System.out.println("sb ="+sb.delete(3,6));
        System.out.println("sb ="+sb.insert(3,"abc"));
        System.out.println("sb ="+sb.replace(6, sb.length(), "END"));

        System.out.println("capacity="+sb.capacity());
        System.out.println("length="+sb.length());
    }
}
```
```
sb =0123456789.0
sb2=0123456789.0
sb3=0123456789.0
sb =01234567890
sb =01267890
sb =012abc67890
sb =012abcEND
capacity=18
length=9

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
Math 클래스에 관련된 예제는 다음과 같이 3개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-20
```java
import static java.lang.Math.*;
import static java.lang.System.*;

class MathEx1 {
    public static void main(String args[]) {
        double val = 90.7552;
        out.println("round("+ val +")=" + round(val));  // 반올림

        val *= 100;
        out.println("round("+ val +")=" + round(val));  // 반올림

        out.println("round("+ val +")/100  =" + round(val)/100);  // 반올림
        out.println("round("+ val +")/100.0=" + round(val)/100.0);  // 반올림
        out.println();
        out.printf("ceil(%3.1f)=%3.1f%n",  1.1, ceil(1.1));   // 올림
        out.printf("floor(%3.1f)=%3.1f%n", 1.5, floor(1.5));  // 버림
        out.printf("round(%3.1f)=%d%n",    1.1, round(1.1));  // 반올림
        out.printf("round(%3.1f)=%d%n",    1.5, round(1.5));  // 반올림
        out.printf("rint(%3.1f)=%f%n",     1.5, rint(1.5));   // 반올림
        out.printf("round(%3.1f)=%d%n",   -1.5, round(-1.5)); // 반올림
        out.printf("rint(%3.1f)=%f%n",    -1.5, rint(-1.5));  // 반올림
        out.printf("ceil(%3.1f)=%f%n",    -1.5, ceil(-1.5));  // 올림
        out.printf("floor(%3.1f)=%f%n",   -1.5, floor(-1.5)); // 버림
    }
}
```
```
round(90.7552)=91
round(9075.52)=9076
round(9075.52)/100  =90
round(9075.52)/100.0=90.76

ceil(1.1)=2.0
floor(1.5)=1.0
round(1.1)=1
round(1.5)=2
rint(1.5)=2.000000
round(-1.5)=-1
rint(-1.5)=-2.000000
ceil(-1.5)=-1.000000
floor(-1.5)=-2.000000

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-21
```java
import static java.lang.Math.*;
import static java.lang.System.*;

class MathEx2 {
    public static void main(String args[]) {
        int i = Integer.MIN_VALUE;

        out.println("i ="+i);
        out.println("-i="+(-i));

        try {
            out.printf("negateExact(%d)= %d%n",  10, negateExact(10));
            out.printf("negateExact(%d)= %d%n", -10, negateExact(-10));

            out.printf("negateExact(%d)= %d%n", i, negateExact(i)); // 예외발생
        } catch(ArithmeticException e) {
            // i를 long타입으로 형변환 다음에 negateExact(long a)를 호출
            out.printf("negateExact(%d)= %d%n",(long)i,negateExact((long)i));
        }
    } // main의 끝
}
```
```
i =-2147483648
-i=-2147483648
negateExact(10)= -10
negateExact(-10)= 10
negateExact(-2147483648)= 2147483648

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-22
```java
import static java.lang.Math.*;
import static java.lang.System.*;

class MathEx3 {
    public static void main(String args[]) {
        int x1=1, y1=1;  // (1, 1)
        int x2=2, y2=2;  // (2, 2)

        double c = sqrt(pow(x2-x1, 2) + pow(y2-y1, 2));
        double a = c * sin(PI/4);  // PI/4 rad = 45 degree
        double b = c * cos(PI/4);
//		double b = c * cos(toRadians(45));

        out.printf("a=%f%n", a);
        out.printf("b=%f%n", b);
        out.printf("c=%f%n", c);
        out.printf("angle=%f rad%n", atan2(a,b));
        out.printf("angle=%f degree%n%n", atan2(a,b) * 180 / PI);
//		out.printf("angle=%f degree%n%n", toDegrees(atan2(a,b)));

        out.printf("24 * log10(2)=%f%n",   24 * log10(2));  // 7.224720
        out.printf("53 * log10(2)=%f%n%n", 53 * log10(2));  // 15.954590
    }
}
```
```
a=1.000000
b=1.000000
c=1.414214
angle=0.785398 rad
angle=45.000000 degree

24 * log10(2)=7.224720
53 * log10(2)=15.954590


Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
래퍼 클래스의 예시는 다음과 같이 3개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-23
```java
class WrapperEx1 {
    public static void main(String[] args) {
        Integer i  = new Integer(100);
        Integer i2 = new Integer(100);

        System.out.println("i==i2 ? "+(i==i2));
        System.out.println("i.equals(i2) ? "+i.equals(i2));
        System.out.println("i.compareTo(i2)="+i.compareTo(i2));
        System.out.println("i.toString()="+i.toString());

        System.out.println("MAX_VALUE="+Integer.MAX_VALUE);
        System.out.println("MIN_VALUE="+Integer.MIN_VALUE);
        System.out.println("SIZE=" +Integer.SIZE+" bits");
        System.out.println("BYTES="+Integer.BYTES+" bytes");
        System.out.println("TYPE=" +Integer.TYPE);
    }
}
```
```
i==i2 ? false
i.equals(i2) ? true
i.compareTo(i2)=0
i.toString()=100
MAX_VALUE=2147483647
MIN_VALUE=-2147483648
SIZE=32 bits
BYTES=4 bytes
TYPE=int

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-24
```java
class WrapperEx2 {
    public static void main(String[] args) {
        int		 i  = new Integer("100").intValue();
        int		 i2 = Integer.parseInt("100");
        Integer  i3 = Integer.valueOf("100");

        int i4 = Integer.parseInt("100",2);
        int i5 = Integer.parseInt("100",8);
        int i6 = Integer.parseInt("100",16);
        int i7 = Integer.parseInt("FF", 16);
//		int i8 = Integer.parseInt("FF");  // NumberFormatException발생

        Integer i9 = Integer.valueOf("100",2);
        Integer i10 = Integer.valueOf("100",8);
        Integer i11 = Integer.valueOf("100",16);
        Integer i12 = Integer.valueOf("FF",16);
//		Integer i13 = Integer.valueOf("FF"); // NumberFormatException발생

        System.out.println(i);
        System.out.println(i2);
        System.out.println(i3);
        System.out.println("100(2) -> "+i4);
        System.out.println("100(8) -> "+i5);
        System.out.println("100(16)-> "+i6);
        System.out.println("FF(16) -> "+i7);

        System.out.println("100(2) -> "+i9);
        System.out.println("100(8) -> "+i10);
        System.out.println("100(16)-> "+i11);
        System.out.println("FF(16) -> "+i12);
    }
}
```
```
100
100
100
100(2) -> 4
100(8) -> 64
100(16)-> 256
FF(16) -> 255
100(2) -> 4
100(8) -> 64
100(16)-> 256
FF(16) -> 255

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-25
```java
class WrapperEx3 {
    public static void main(String[] args) {
        int i = 10;

        // 기본형을 참조형으로 형변환(형변환 생략가능)
        Integer intg = (Integer)i; // Integer intg = Integer.valueOf(i);
        Object   obj = (Object)i;  // Object obj = (Object)Integer.valueOf(i);

        Long     lng = 100L;  // Long lng = new Long(100L);

        int i2 = intg + 10;   // 참조형과 기본형간의 연산 가능
        long l = intg + lng;  // 참조형 간의 덧셈도 가능

        Integer intg2 = new Integer(20);
        int i3 = (int)intg2;  // 참조형을 기본형으로 형변환도 가능(형변환 생략가능)

        Integer intg3 = intg2 + i3;

        System.out.println("i     ="+i);
        System.out.println("intg  ="+intg);
        System.out.println("obj   ="+obj);
        System.out.println("lng   ="+lng);
        System.out.println("intg + 10  ="+i2);
        System.out.println("intg + lng ="+l);
        System.out.println("intg2 ="+intg2);
        System.out.println("i3    ="+i3);
        System.out.println("intg2 + i3 ="+intg3);
    }
}
```
```
i     =10
intg  =10
obj   =10
lng   =100
intg + 10  =20
intg + lng =110
intg2 =20
i3    =20
intg2 + i3 =40
```
________________________________________________________________________________________________________________________________________________________________________
java.util.Objects 클래스에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-26
```java
import java.util.*;
import static java.util.Objects.*;  // Objects클래스의 메서드를 static import

class ObjectsTest {
    public static void main(String[] args) {
        String[][] str2D   = new String[][]{{"aaa","bbb"},{"AAA","BBB"}};
        String[][] str2D_2 = new String[][]{{"aaa","bbb"},{"AAA","BBB"}};

        System.out.print("str2D  ={");
        for(String[] tmp : str2D)
            System.out.print(Arrays.toString(tmp));
        System.out.println("}");

        System.out.print("str2D_2={");
        for(String[] tmp : str2D_2)
            System.out.print(Arrays.toString(tmp));
        System.out.println("}");

        System.out.println("equals(str2D, str2D_2)="+Objects.equals(str2D, str2D_2));
        System.out.println("deepEquals(str2D, str2D_2)="+Objects.deepEquals(str2D, str2D_2));

        System.out.println("isNull(null) ="+isNull(null));
        System.out.println("nonNull(null)="+nonNull(null));
        System.out.println("hashCode(null)="+Objects.hashCode(null));
        System.out.println("toString(null)="+Objects.toString(null));
        System.out.println("toString(null, \"\")="+Objects.toString(null, ""));

        Comparator c = String.CASE_INSENSITIVE_ORDER;

        System.out.println("compare(\"aa\",\"bb\")="+compare("aa","bb",c));
        System.out.println("compare(\"bb\",\"aa\")="+compare("bb","aa",c));
        System.out.println("compare(\"ab\",\"AB\")="+compare("ab","AB",c));
    }
}
```
```
str2D  ={[aaa, bbb][AAA, BBB]}
str2D_2={[aaa, bbb][AAA, BBB]}
equals(str2D, str2D_2)=false
deepEquals(str2D, str2D_2)=true
isNull(null) =true
nonNull(null)=false
hashCode(null)=0
toString(null)=null
toString(null, "")=
compare("aa","bb")=-1
compare("bb","aa")=1
compare("ab","AB")=0

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
java.util.Random 클래스의 예시는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-27
```java
import java.util.*;

class RandomEx1 {
    public static void main(String args[]) {
        Random rand  = new Random(1);
        Random rand2 = new Random(1);

        System.out.println("= rand =");
        for(int i=0; i < 5; i++)
            System.out.println(i + ":" + rand.nextInt());

        System.out.println();
        System.out.println("= rand2 =");
        for(int i=0; i < 5; i++)
            System.out.println(i + ":" + rand2.nextInt());
    }
}
```
```
= rand =
0:-1155869325
1:431529176
2:1761283695
3:1749940626
4:892128508

= rand2 =
0:-1155869325
1:431529176
2:1761283695
3:1749940626
4:892128508

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-28
```java
import java.util.*;

class RandomEx2 {
    public static void main(String[] args) {
        Random rand = new Random();
        int[] number = new int[100];
        int[] counter = new int[10];

        for (int i=0; i < number.length ; i++ ) {
//			System.out.print(number[i] = (int)(Math.random() * 10));
//                  0<=x<10 범위의 정수 x를 반환한다.
            System.out.print(number[i] = rand.nextInt(10));
        }
        System.out.println();

        for (int i=0; i < number.length ; i++ ) {
            counter[number[i]]++;
        }

        for (int i=0; i < counter.length ; i++ ) {
            System.out.println( i +"의 개수 :"+ printGraph('#',counter[i]) + " " + counter[i]);
        }
    }

    public static String printGraph(char ch, int value) {
        char[] bar = new char[value];

        for(int i=0; i < bar.length; i++) {
            bar[i] = ch;
        }
        return new String(bar);
    }
} 
```
```
8810667382518226255986617702046562715446210266054428744286292704413520111380352841635361381008214088
0의 개수 :########### 11
1의 개수 :############# 13
2의 개수 :############### 15
3의 개수 :####### 7
4의 개수 :########### 11
5의 개수 :######### 9
6의 개수 :############# 13
7의 개수 :###### 6
8의 개수 :############# 13
9의 개수 :## 2

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-29
```java
import java.util.*;

class RandomEx3 {
    public static void main(String[] args) {
        for(int i=0; i < 10;i++)
            System.out.print(getRand(5, 10)+",");
        System.out.println();

        int[] result = fillRand(new int[10], new int[]{ 2, 3, 7, 5});

        System.out.println(Arrays.toString(result));
    }

    public static int[] fillRand(int[] arr, int from, int to) {
        for(int i=0; i < arr.length; i++) {
            arr[i] = getRand(from, to);
        }

        return arr;
    }

    public static int[] fillRand(int[] arr, int[] data) {
        for(int i=0; i < arr.length; i++) {
            arr[i] = data[getRand(0, data.length-1)];
        }

        return arr;
    }

    public static int getRand(int from, int to) {
        return (int)(Math.random() * (Math.abs(to-from)+1)) + Math.min(from, to);
    }
}
```
```
7,5,6,9,8,9,7,5,7,5,
[3, 3, 5, 7, 5, 5, 3, 5, 5, 7]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-30
```java
class RandomEx4 {
    final static int RECORD_NUM = 10;	// 생성할 레코드의 수를 정한다.
    final static String TABLE_NAME = "TEST_TABLE";
    final static String[] CODE1 = {"010","011","017","018","019"};
    final static String[] CODE2 = {"남자", "여자"};
    final static String[] CODE3 = {"10대","20대","30대","40대","50대"};

    public static void main(String[] args) {
        for(int i=0; i < RECORD_NUM; i++) {
            System.out.println(" INSERT INTO "+TABLE_NAME
                    +  " VALUES ("
                    +  " '" + getRandArr(CODE1) + "'"
                    +  ", '" + getRandArr(CODE2) + "'"
                    +  ", '" + getRandArr(CODE3) + "'"
                    +  ",  " + getRand(100, 200)	// 100~200 사이의 값을 얻는다.
                    + "); ");
        }
    }

    public static String getRandArr(String[] arr) {
        return arr[getRand(arr.length-1)];	// 배열에 저장된 값 중 하나를 반환한다.
    }

    public static int getRand(int n) {
        return getRand(0, n);
    }

    public static int getRand(int from, int to) {
        return (int)(Math.random()*(Math.abs(to-from)+1))+Math.min(from, to);
    }
}
```
```
 INSERT INTO TEST_TABLE VALUES ( '019', '남자', '40대',  114); 
 INSERT INTO TEST_TABLE VALUES ( '019', '여자', '20대',  193); 
 INSERT INTO TEST_TABLE VALUES ( '019', '여자', '20대',  147); 
 INSERT INTO TEST_TABLE VALUES ( '010', '여자', '10대',  109); 
 INSERT INTO TEST_TABLE VALUES ( '011', '여자', '40대',  160); 
 INSERT INTO TEST_TABLE VALUES ( '011', '남자', '40대',  190); 
 INSERT INTO TEST_TABLE VALUES ( '010', '여자', '20대',  159); 
 INSERT INTO TEST_TABLE VALUES ( '019', '여자', '30대',  189); 
 INSERT INTO TEST_TABLE VALUES ( '010', '남자', '30대',  104); 
 INSERT INTO TEST_TABLE VALUES ( '011', '여자', '40대',  170); 

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
정규식을 활용하는 java.util.regex 패키지와 관련된 예시는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-31
```java
import java.util.regex.*;	// Pattern과 Matcher가 속한 패키지

class RegularEx1 {
    public static void main(String[] args)
    {
        String[] data = {"bat", "baby", "bonus",
                "cA","ca", "co", "c.", "c0", "car","combat","count",
                "date", "disc"};
        Pattern p = Pattern.compile("c[a-z]*");	// c로 시작하는 소문자 영단어

        for(int i=0; i < data.length; i++) {
            Matcher m = p.matcher(data[i]);
            if(m.matches())
                System.out.print(data[i] + ",");
        }
    }
}
```
```
ca,co,car,combat,count,
Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-32
```java
import java.util.regex.*;	// Pattern과 Matcher가 속한 패키지

class RegularEx2 {
    public static void main(String[] args) {
        String[] data = {"bat", "baby", "bonus", "c", "cA",
                "ca", "co", "c.", "c0", "c#",
                "car","combat","count", "date", "disc"
        };
        String[] pattern = {".*","c[a-z]*","c[a-z]", "c[a-zA-Z]",
                "c[a-zA-Z0-9]","c.","c.*","c\\.","c\\w",
                "c\\d","c.*t", "[b|c].*", ".*a.*", ".*a.+",
                "[b|c].{2}"
        };

        for(int x=0; x < pattern.length; x++) {
            Pattern p = Pattern.compile(pattern[x]);
            System.out.print("Pattern : " + pattern[x] + "  결과: ");
            for(int i=0; i < data.length; i++) {
                Matcher m = p.matcher(data[i]);
                if(m.matches())
                    System.out.print(data[i] + ",");
            }
            System.out.println();
        }
    } // public static void main(String[] args)
}
```
```
Pattern : .*  결과: bat,baby,bonus,c,cA,ca,co,c.,c0,c#,car,combat,count,date,disc,
Pattern : c[a-z]*  결과: c,ca,co,car,combat,count,
Pattern : c[a-z]  결과: ca,co,
Pattern : c[a-zA-Z]  결과: cA,ca,co,
Pattern : c[a-zA-Z0-9]  결과: cA,ca,co,c0,
Pattern : c.  결과: cA,ca,co,c.,c0,c#,
Pattern : c.*  결과: c,cA,ca,co,c.,c0,c#,car,combat,count,
Pattern : c\.  결과: c.,
Pattern : c\w  결과: cA,ca,co,c0,
Pattern : c\d  결과: c0,
Pattern : c.*t  결과: combat,count,
Pattern : [b|c].*  결과: bat,baby,bonus,c,cA,ca,co,c.,c0,c#,car,combat,count,
Pattern : .*a.*  결과: bat,baby,ca,car,combat,date,
Pattern : .*a.+  결과: bat,baby,car,combat,date,
Pattern : [b|c].{2}  결과: bat,car,

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-33
```java
import java.util.regex.*;	// Pattern과 Matcher가 속한 패키지

class RegularEx3{
    public static void main(String[] args) {
        String source  = "HP:011-1111-1111, HOME:02-999-9999 ";
        String pattern = "(0\\d{1,2})-(\\d{3,4})-(\\d{4})";

        Pattern p = Pattern.compile(pattern);
        Matcher m = p.matcher(source);

        int i=0;

        while(m.find()) {
            System.out.println( ++i + ": " + m.group() + " -> "+ m.group(1) +", "+ m.group(2)+", "+ m.group(3));
        }
    } // main의 끝
}
```
```
1: 011-1111-1111 -> 011, 1111, 1111
2: 02-999-9999 -> 02, 999, 9999

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-34
```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

class RegularEx4 {
    public static void main(String[] args) {
        String source  = "A broken hand works, but not a broken heart.";
        String pattern = "broken";

        StringBuffer sb = new StringBuffer();

        Pattern p = Pattern.compile(pattern);
        Matcher m = p.matcher(source);
        System.out.println("source:"+source);

        int i=0;

        while(m.find()) {
            System.out.println(++i + "번째 매칭:" + m.start() + "~"+ m.end());

            // broken을 drunken으로 치환하여 sb에 저장한다.
            m.appendReplacement(sb, "drunken");
        }

        m.appendTail(sb);
        System.out.println("Replacement count : " + i);
        System.out.println("result:"+sb.toString());
    }
}
```
```
source:A broken hand works, but not a broken heart.
1번째 매칭:2~8
2번째 매칭:31~37
Replacement count : 2
result:A drunken hand works, but not a drunken heart.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
java.util.Scanner 클래스에 대한 예시는 총 3개가 수록되어 있으나, 여기에서는 예제 9-35만 다루도록 하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-35
```java
import java.util.*;

class ScannerEx1 {
    public static void main(String[] args) {
        Scanner s = new Scanner(System.in);
        String[] argArr = null;

        while(true) {
            String prompt = ">>";
            System.out.print(prompt);

            // 화면으로부터 라인단위로 입력받는다.
            String input = s.nextLine();

            input = input.trim();        // 입력받은 값에서 불필요한 앞뒤 공백을 제거한다.
            argArr = input.split(" +");  // 입력받은 내용을 공백을 구분자로 자른다.

            String command = argArr[0].trim();

            if("".equals(command)) continue;

            // 명령어를 소문자로 바꾼다.
            command = command.toLowerCase();

            // q 또는 Q를 입력하면 실행종료한다.
            if(command.equals("q")) {
                System.exit(0);
            } else {
                for(int i=0; i < argArr.length;i++)
                    System.out.println(argArr[i]);
            }
        } // while(true)
    } // main
}
```
```
>>hello
hello
>>hello 123
hello
123
>>hello 123 456
hello
123
456
>>
>>q

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
java.util.StringTokenizer 클래스와 관련된 예제는 다음과 같이 5개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-38
```java
import java.util.*;

class StringTokenizerEx1 {
    public static void main(String[] args){
        String source = "100,200,300,400";
        StringTokenizer st = new StringTokenizer(source, ",");

        while(st.hasMoreTokens()){
            System.out.println(st.nextToken());
        }
    } // main의 끝
} 
```
```
100
200
300
400

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-39
```java
import java.util.*;

class StringTokenizerEx2 {
    public static void main(String[] args) {
        String expression = "x=100*(200+300)/2";
        StringTokenizer st = new StringTokenizer(expression, "+-*/=()", true);

        while(st.hasMoreTokens()){
            System.out.println(st.nextToken());
        }
    } // main의 끝
} 
```
```
x
=
100
*
(
200
+
300
)
/
2

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-40
```java
import java.util.*;

class StringTokenizerEx3 {
    public static void main(String args[]) {
        String source = "1,김천재,100,100,100|2,박수재,95,80,90|3,이자바,80,90,90";
        StringTokenizer st = new StringTokenizer(source, "|");

        while(st.hasMoreTokens()) {
            String token = st.nextToken();

            StringTokenizer st2 = new StringTokenizer(token, ",");
            while(st2.hasMoreTokens()) {
                System.out.println(st2.nextToken());
            }
            System.out.println("------");
        }
    } // main의 끝
}
```
```
1
김천재
100
100
100
------
2
박수재
95
80
90
------
3
이자바
80
90
90
------

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-41
```java
import java.util.*;

class StringTokenizerEx4 {
    public static void main(String args[]) {
        String input = "삼십만삼천백십오";

        System.out.println(input);
        System.out.println(hangulToNum(input));
    }

    public static long hangulToNum(String input) {
        long result = 0;     // 최종 변환결과를 저장하기 위한 변수
        long tmpResult = 0;  // 십백천 단위의 값을 저장하기 위한 임시변수
        long num = 0;

        final String NUMBER = "영일이삼사오육칠팔구";
        final String UNIT   = "십백천만억조";
        final long[] UNIT_NUM  = {10,100,1000,10000,(long)1e8,(long)1e12};

        StringTokenizer st = new StringTokenizer(input, UNIT, true);

        while(st.hasMoreTokens()) {
            String token = st.nextToken();
            int check = NUMBER.indexOf(token);

            if(check==-1) { // 단위인 경우
                if("만억조".indexOf(token)==-1) {
                    tmpResult += ( num!=0 ? num : 1) * UNIT_NUM[UNIT.indexOf(token)];
                } else {
                    tmpResult += num;
                    result += (tmpResult!=0 ? tmpResult : 1) * UNIT_NUM[UNIT.indexOf(token)];
                    tmpResult = 0;
                }
                num = 0;
            } else {  // 숫자인 경우
                num = check;
            }
        } // end of while

        return result + tmpResult + num;
    }
}
```
```
삼십만삼천백십오
303115

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-42
```java
import java.util.*;

class StringTokenizerEx5 {
    public static void main(String[] args) {
        String data = "100,,,200,300";

        String[] result = data.split(",");
        StringTokenizer st = new StringTokenizer(data, ",");

        for(int i=0; i < result.length;i++)
            System.out.print(result[i]+"|");

        System.out.println("개수:"+result.length);

        int i=0;
        for(;st.hasMoreTokens();i++)
            System.out.print(st.nextToken()+"|");

        System.out.println("개수:"+i);
    } // main
}
```
```
100|||200|300|개수:5
100|200|300|개수:3

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
java.math.BigInteger 클래스에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-43
```java
import java.math.*;

class BigIntegerEx {
    public static void main(String[] args) throws Exception {
        BigInteger big =  BigInteger.ONE;

        for(int i=1; i<100;i++) { // 1!부터 99!까지 출력
            System.out.printf("%d!=%s%n", i, calcFactorial(i));
            Thread.sleep(300); // 0.3초의 지연
        }
    }

    static String calcFactorial(int n) {
        return factorial(BigInteger.valueOf(n)).toString();
    }

    static BigInteger factorial(BigInteger n) {
        if(n.equals(BigInteger.ZERO)) {
            return BigInteger.ONE;
        } else {  // return n * factorial(n-1);
            return n.multiply(factorial(n.subtract(BigInteger.ONE)));
        }
    }
}
```
```
1!=1
2!=2
3!=6
... 
97!=96192759682482119853328425949563698712343813919172976158104477319333745612481875498805879175589072651261284189679678167647067832320000000000000000000000
98!=9426890448883247745626185743057242473809693764078951663494238777294707070023223798882976159207729119823605850588608460429412647567360000000000000000000000
99!=933262154439441526816992388562667004907159682643816214685929638952175999932299156089414639761565182862536979208272237582511852109168640000000000000000000000

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
java.math.BigDecimal 클래스에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 9-44
```java
import java.math.*;
import static java.math.RoundingMode.*;

class BigDecimalEx {
    public static void main(String[] args) throws Exception {
        BigDecimal bd1 = new BigDecimal("123.456");
        BigDecimal bd2 = new BigDecimal("1.0");

        System.out.print("bd1="+bd1);
        System.out.print(",\tvalue="+bd1.unscaledValue());
        System.out.print(",\tscale="+bd1.scale());
        System.out.print(",\tprecision="+bd1.precision());
        System.out.println();

        System.out.print("bd2="+bd2);
        System.out.print(",\tvalue="+bd2.unscaledValue());
        System.out.print(",\tscale="+bd2.scale());
        System.out.print(",\tprecision="+bd2.precision());
        System.out.println();


        BigDecimal bd3 = bd1.multiply(bd2);
        System.out.print("bd3="+bd3);
        System.out.print(",\tvalue="+bd3.unscaledValue());
        System.out.print(",\tscale="+bd3.scale());
        System.out.print(",\tprecision="+bd3.precision());
        System.out.println();

        System.out.println(bd1.divide(bd2, 2, HALF_UP)); // 123.46
        System.out.println(bd1.setScale(2, HALF_UP));    // 123.46
        System.out.println(bd1.divide(bd2, new MathContext(2, HALF_UP)));
    }
}
```
```
bd1=123.456,	value=123456,	scale=3,	precision=6
bd2=1.0,	value=10,	scale=1,	precision=2
bd3=123.4560,	value=1234560,	scale=4,	precision=7
123.46
123.46
1.2E+2

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
이상으로 java.lang패키지와 이와 관련된 유용한 클래스에 대해 알아보았습니다. 여기까지 자바의 기본적인 기능에 대해 알아보았고, 다음부터는 3개의 포스팅에 걸쳐 자바 심화 개념에 대해 다룰 예정입니다. 오늘도 긴 글 읽어주시느라 모두 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=