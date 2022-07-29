---
title: "자바 공부 기록(10) - 지네릭스, 열거형, 애너테이션에 대해 알아볼까요?"
date: 2022-07-29T13:33:53+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅에서는 지네릭스, 열거형, 애너테이션에 대해 알아보겠습니다. 시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 12장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
________________________________________________________________________________________________________________________________________________________________________
우선 지네릭스란 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에 컴파일 시의 타입체크를 해주는 기능입니다. 객체의 타입을 컴파일 시에 체크하기 때문에 객체의 타입 안정성을 높이고 형변환의 번거로움이 줄어들 수 있습니다. 그러면 이러한 지네릭스를 어떻게 활용하는지 지금부터 알아보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
우선 지네릭 클래스의 객체 생성과 사용에 대해 알아보겠습니다. 사용 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-1
```java
import java.util.ArrayList;

class Fruit				  { public String toString() { return "Fruit";}}
class Apple extends Fruit { public String toString() { return "Apple";}}
class Grape extends Fruit { public String toString() { return "Grape";}}
class Toy		          { public String toString() { return "Toy"  ;}}

class FruitBoxEx1 {
    public static void main(String[] args) {
        Box<Fruit> fruitBox = new Box<Fruit>();
        Box<Apple> appleBox = new Box<Apple>();
        Box<Toy>   toyBox   = new Box<Toy>();
//		Box<Grape> grapeBox = new Box<Apple>(); // 에러. 타입 불일치

        fruitBox.add(new Fruit());
        fruitBox.add(new Apple()); // OK. void add(Fruit item)

        appleBox.add(new Apple());
        appleBox.add(new Apple());
//		appleBox.add(new Toy()); // 에러. Box<Apple>에는 Apple만 담을 수 있음

        toyBox.add(new Toy());
//		toyBox.add(new Apple()); // 에러. Box<Toy>에는 Apple을 담을 수 없음

        System.out.println(fruitBox);
        System.out.println(appleBox);
        System.out.println(toyBox);
    }  // main의 끝
}

class Box<T> {
    ArrayList<T> list = new ArrayList<T>();
    void add(T item)  { list.add(item); }
    T get(int i)      { return list.get(i); }
    int size() { return list.size(); }
    public String toString() { return list.toString();}
}
```
```
[Fruit, Apple]
[Apple, Apple]
[Toy]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
제한된 지네릭 클래스에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-2
```java
import java.util.ArrayList;

class Fruit implements Eatable {
    public String toString() { return "Fruit";}
}
class Apple extends Fruit { public String toString() { return "Apple";}}
class Grape extends Fruit { public String toString() { return "Grape";}}
class Toy		          { public String toString() { return "Toy"  ;}}

interface Eatable {}

class FruitBoxEx2 {
    public static void main(String[] args) {
        FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
        FruitBox<Apple> appleBox = new FruitBox<Apple>();
        FruitBox<Grape> grapeBox = new FruitBox<Grape>();
//		FruitBox<Grape> grapeBox = new FruitBox<Apple>(); // 에러. 타입 불일치
//		FruitBox<Toy>   toyBox    = new FruitBox<Toy>();   // 에러.

        fruitBox.add(new Fruit());
        fruitBox.add(new Apple());
        fruitBox.add(new Grape());
        appleBox.add(new Apple());
//		appleBox.add(new Grape());  // 에러. Grape는 Apple의 자손이 아님
        grapeBox.add(new Grape());

        System.out.println("fruitBox-"+fruitBox);
        System.out.println("appleBox-"+appleBox);
        System.out.println("grapeBox-"+grapeBox);
    }  // main
}

class FruitBox<T extends Fruit & Eatable> extends Box<T> {}

class Box<T> {
    ArrayList<T> list = new ArrayList<T>();
    void add(T item)  { list.add(item);      }
    T get(int i)      { return list.get(i); }
    int size()        { return list.size();  }
    public String toString() { return list.toString();}
}
```
```
fruitBox-[Fruit, Apple, Grape]
appleBox-[Apple]
grapeBox-[Grape]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
와일드 카드에 관련된 예시는 다음 두개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-3
```java
import java.util.ArrayList;

class Fruit		          { public String toString() { return "Fruit";}}
class Apple extends Fruit { public String toString() { return "Apple";}}
class Grape extends Fruit { public String toString() { return "Grape";}}

class Juice {
    String name;

    Juice(String name)	     { this.name = name + "Juice"; }
    public String toString() { return name;		 }
}

class Juicer {
    static Juice makeJuice(FruitBox<? extends Fruit> box) {
        String tmp = "";

        for(Fruit f : box.getList())
            tmp += f + " ";
        return new Juice(tmp);
    }
}

class FruitBoxEx3 {
    public static void main(String[] args) {
        FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
        FruitBox<Apple> appleBox = new FruitBox<Apple>();

        fruitBox.add(new Apple());
        fruitBox.add(new Grape());
        appleBox.add(new Apple());
        appleBox.add(new Apple());

        System.out.println(Juicer.makeJuice(fruitBox));
        System.out.println(Juicer.makeJuice(appleBox));
    }  // main
}

class FruitBox<T extends Fruit> extends Box<T> {}

class Box<T> {
    //class FruitBox<T extends Fruit> {
    ArrayList<T> list = new ArrayList<T>();
    void add(T item) { list.add(item);      }
    T get(int i)     { return list.get(i); }
    ArrayList<T> getList() { return list;  }
    int size()       { return list.size(); }
    public String toString() { return list.toString();}
}
```
```
Apple Grape Juice
Apple Apple Juice

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-4
```java
import java.util.*;

class Fruit	{
    String name;
    int weight;

    Fruit(String name, int weight) {
        this.name   = name;
        this.weight = weight;
    }

    public String toString() { return name+"("+weight+")";}

}

class Apple extends Fruit {
    Apple(String name, int weight) {
        super(name, weight);
    }
}

class Grape extends Fruit {
    Grape(String name, int weight) {
        super(name, weight);
    }
}

class AppleComp implements Comparator<Apple> {
    public int compare(Apple t1, Apple t2) {
        return t2.weight - t1.weight;
    }
}

class GrapeComp implements Comparator<Grape> {
    public int compare(Grape t1, Grape t2) {
        return t2.weight - t1.weight;
    }
}

class FruitComp implements Comparator<Fruit> {
    public int compare(Fruit t1, Fruit t2) {
        return t1.weight - t2.weight;
    }
}

class FruitBoxEx4 {
    public static void main(String[] args) {
        FruitBox<Apple> appleBox = new FruitBox<Apple>();
        FruitBox<Grape> grapeBox = new FruitBox<Grape>();

        appleBox.add(new Apple("GreenApple", 300));
        appleBox.add(new Apple("GreenApple", 100));
        appleBox.add(new Apple("GreenApple", 200));

        grapeBox.add(new Grape("GreenGrape", 400));
        grapeBox.add(new Grape("GreenGrape", 300));
        grapeBox.add(new Grape("GreenGrape", 200));

        Collections.sort(appleBox.getList(), new AppleComp());
        Collections.sort(grapeBox.getList(), new GrapeComp());
        System.out.println(appleBox);
        System.out.println(grapeBox);
        System.out.println();
        Collections.sort(appleBox.getList(), new FruitComp());
        Collections.sort(grapeBox.getList(), new FruitComp());
        System.out.println(appleBox);
        System.out.println(grapeBox);
    }  // main
}

class FruitBox<T extends Fruit> extends Box<T> {}

class Box<T> {
    ArrayList<T> list = new ArrayList<T>();

    void add(T item) {
        list.add(item);
    }

    T get(int i) {
        return list.get(i);
    }

    ArrayList<T> getList() { return list; }

    int size() {
        return list.size();
    }

    public String toString() {
        return list.toString();
    }
}
```
```
[GreenApple(300), GreenApple(200), GreenApple(100)]
[GreenGrape(400), GreenGrape(300), GreenGrape(200)]

[GreenApple(100), GreenApple(200), GreenApple(300)]
[GreenGrape(200), GreenGrape(300), GreenGrape(400)]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 열거형에 대해 확인해보겠습니다. 열거형이란 서로 관련된 상수를 편리하게 선언하기 위한 것으로 여러 상수를 정의할 때 사용하면 유용합니다. 원래 자바는 C언어와 달리 열거형이라는 것이 존재하지 않았으나 JDK1.5부터 새로 추가되었습니다. 자바의 열거형은 C언어의 열거형보다 더 향상된 것으로 열거형이 갖는 값뿐만 아니라 타입도 관리하기 때문에 보다 논리적인 오류를 줄일 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
이러한 열거형을 정의하고 사용하는 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-5
```java
enum Direction { EAST, SOUTH, WEST, NORTH }

class EnumEx1 {
    public static void main(String[] args) {
        Direction d1 = Direction.EAST;
        Direction d2 = Direction.valueOf("WEST");
        Direction d3 = Enum.valueOf(Direction.class, "EAST");

        System.out.println("d1="+d1);
        System.out.println("d2="+d2);
        System.out.println("d3="+d3);

        System.out.println("d1==d2 ? "+ (d1==d2));
        System.out.println("d1==d3 ? "+ (d1==d3));
        System.out.println("d1.equals(d3) ? "+ d1.equals(d3));
//		System.out.println("d2 > d3 ? "+ (d1 > d3)); // 에러
        System.out.println("d1.compareTo(d3) ? "+ (d1.compareTo(d3)));
        System.out.println("d1.compareTo(d2) ? "+ (d1.compareTo(d2)));

        switch(d1) {
            case EAST: // Direction.EAST라고 쓸 수 있다.
                System.out.println("The direction is EAST.");
                break;
            case SOUTH:
                System.out.println("The direction is SOUTH.");
                break;
            case WEST:
                System.out.println("The direction is WEST.");
                break;
            case NORTH:
                System.out.println("The direction is NORTH.");
                break;
            default:
                System.out.println("Invalid direction.");
//				break;
        }

        Direction[] dArr = Direction.values();

        for(Direction d : dArr)  // for(Direction d : Direction.values())
            System.out.printf("%s=%d%n", d.name(), d.ordinal());
    }
}
```
```
d1=EAST
d2=WEST
d3=EAST
d1==d2 ? false
d1==d3 ? true
d1.equals(d3) ? true
d1.compareTo(d3) ? 0
d1.compareTo(d2) ? -2
The direction is EAST.
EAST=0
SOUTH=1
WEST=2
NORTH=3

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
열거형에 멤버를 추가하는 예시는 다음 두개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-6
```java
enum Direction {
    EAST(1, ">"), SOUTH(2,"V"), WEST(3, "<"), NORTH(4,"^");

    private static final Direction[] DIR_ARR = Direction.values();
    private final int value;
    private final String symbol;

    Direction(int value, String symbol) { // private Direction(int value)
        this.value  = value;
        this.symbol = symbol;
    }

    public int getValue()      { return value;  }
    public String getSymbol()  { return symbol; }

    public static Direction of(int dir) {
        if (dir < 1 || dir > 4) {
            throw new IllegalArgumentException("Invalid value :" + dir);
        }
        return DIR_ARR[dir - 1];
    }

    // 방향을 회전시키는 메서드. num의 값만큼 90도씩 시계방향으로 회전한다.
    public Direction rotate(int num) {
        num = num % 4;

        if(num < 0) num +=4; // num이 음수일 때는 시계반대 방향으로 회전

        return DIR_ARR[(value-1+num) % 4];
    }

    public String toString() {
        return name()+getSymbol();
    }
} // enum Direction

class EnumEx2 {
    public static void main(String[] args) {
        for(Direction d : Direction.values())
            System.out.printf("%s=%d%n", d.name(), d.getValue());

        Direction d1 = Direction.EAST;
        Direction d2 = Direction.of(1);

        System.out.printf("d1=%s, %d%n", d1.name(), d1.getValue());
        System.out.printf("d2=%s, %d%n", d2.name(), d2.getValue());

        System.out.println(Direction.EAST.rotate(1));
        System.out.println(Direction.EAST.rotate(2));
        System.out.println(Direction.EAST.rotate(-1));
        System.out.println(Direction.EAST.rotate(-2));
    }
}
```
```
EAST=1
SOUTH=2
WEST=3
NORTH=4
d1=EAST, 1
d2=EAST, 1
SOUTHV
WEST<
NORTH^
WEST<

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-7
```java
enum Transportation {
    BUS(100)      { int fare(int distance) { return distance*BASIC_FARE;}},
    TRAIN(150)    { int fare(int distance) { return distance*BASIC_FARE;}},
    SHIP(100)     { int fare(int distance) { return distance*BASIC_FARE;}},
    AIRPLANE(300) { int fare(int distance) { return distance*BASIC_FARE;}};

    protected final int BASIC_FARE; // protected로 해야 각 상수에서 접근 가능

    Transportation(int basicFare) { // private Transportation(int basicFare) {
        BASIC_FARE = basicFare;
    }

    public int getBasicFare() { return BASIC_FARE; }

    abstract int fare(int distance); // 거리에 따른 요금 계산
}

class EnumEx3 {
    public static void main(String[] args) {
        System.out.println("bus fare="     +Transportation.BUS.fare(100));
        System.out.println("train fare="   +Transportation.TRAIN.fare(100));
        System.out.println("ship fare="    +Transportation.SHIP.fare(100));
        System.out.println("airplane fare="+Transportation.AIRPLANE.fare(100));
    }
}
```
```
bus fare=10000
train fare=15000
ship fare=10000
airplane fare=30000

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
열거형과 관련된 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-8
```java
abstract class MyEnum<T extends MyEnum<T>> implements Comparable<T> {
    static int id = 0;

    int ordinal;
    String name = "";

    public int ordinal() { return ordinal; }

    MyEnum(String name) {
        this.name = name;
        ordinal = id++;
    }

    public int compareTo(T t) {
        return ordinal - t.ordinal();
    }
}

abstract class MyTransportation extends MyEnum {
    static final MyTransportation BUS   = new MyTransportation("BUS", 100) {
        int fare(int distance) { return distance * BASIC_FARE; }
    };
    static final MyTransportation TRAIN = new MyTransportation("TRAIN", 150) {
        int fare(int distance) { return distance * BASIC_FARE; }
    };
    static final MyTransportation SHIP  = new MyTransportation("SHIP", 100) {
        int fare(int distance) { return distance * BASIC_FARE; }
    };
    static final MyTransportation AIRPLANE =
            new MyTransportation("AIRPLANE", 300) {
                int fare(int distance) { return distance * BASIC_FARE; }
            };

    abstract int fare(int distance); // 추상 메서드

    protected final int BASIC_FARE;

    private MyTransportation(String name, int basicFare) {
        super(name);
        BASIC_FARE = basicFare;
    }

    public String name()     { return name; }
    public String toString() { return name; }
}

class EnumEx4 {
    public static void main(String[] args) {
        MyTransportation t1 = MyTransportation.BUS;
        MyTransportation t2 = MyTransportation.BUS;
        MyTransportation t3 = MyTransportation.TRAIN;
        MyTransportation t4 = MyTransportation.SHIP;
        MyTransportation t5 = MyTransportation.AIRPLANE;

        System.out.printf("t1=%s, %d%n", t1.name(), t1.ordinal());
        System.out.printf("t2=%s, %d%n", t2.name(), t2.ordinal());
        System.out.printf("t3=%s, %d%n", t3.name(), t3.ordinal());
        System.out.printf("t4=%s, %d%n", t4.name(), t4.ordinal());
        System.out.printf("t5=%s, %d%n", t5.name(), t5.ordinal());
        System.out.println("t1==t2 ? "+(t1==t2));
        System.out.println("t1.compareTo(t3)="+ t1.compareTo(t3));
    }
}
```
```
t1=BUS, 0
t2=BUS, 0
t3=TRAIN, 1
t4=SHIP, 2
t5=AIRPLANE, 3
t1==t2 ? true
t1.compareTo(t3)=-1

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 애너테이션에 대해 알아보겠습니다. 애너테이션이란 자바 소스 코드에 추가하여 사용할 수 있는 메타데이터의 일종입니다. 이러한 애너테이션은 JDK에서 기본적으로 제공하는 것과 다른 프로그램에서 제공하는 것들이 있는데, 어느 것이든 그저 약속된 형식으로 정보를 제공하기만 하면 됩니다. JDK에서 제공하는 표준 애너테이션은 주로 컴파일러를 위한 것으로 컴파일러에게 유용한 정보를 제공합니다. 그리고 새로운 애너테이션을 정의할 때 사용하는 메타 애너테이션을 제공합니다. 
________________________________________________________________________________________________________________________________________________________________________
이러한 애너테이션 중 표준 애너테이션에 대한 예시는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-9
```java
class Parent {
    void parentMethod() { }
}

class Child extends Parent {
    @Override
    void parentmethod() { } // 조상 메서드의 이름을 잘못적었음.
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-10
```java
class NewClass{
    int newField;

    int getNewField() {
        return newField;
    }

    @Deprecated
    int oldField;

    @Deprecated
    int getOldField() {
        return oldField;
    }
}

class AnnotationEx2 {
    public static void main(String args[]) {
        NewClass nc = new NewClass();

        nc.oldField = 10;                     //@Depreacted가 붙은 대상을 사용
        System.out.println(nc.getOldField()); //@Depreacted가 붙은 대상을 사용
    }
}
```
```
10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-11
```java
import java.util.ArrayList;

class NewClass{
    int newField;

    int getNewField() {
        return newField;
    }

    @Deprecated
    int oldField;

    @Deprecated
    int getOldField() {
        return oldField;
    }
}


class AnnotationEx3 {
    @SuppressWarnings("deprecation")     // deprecation관련 경고를 억제
    public static void main(String args[]) {
        NewClass nc = new NewClass();

        nc.oldField = 10;                     //@Depreacted가 붙은 대상을 사용
        System.out.println(nc.getOldField()); //@Depreacted가 붙은 대상을 사용

        @SuppressWarnings("unchecked")               // 지네릭스 관련 경고를 억제
        ArrayList<NewClass> list = new ArrayList();  // 타입을 지정하지 않음.
        list.add(nc);
    }
}
```
```
10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-12
```java
import java.util.Arrays;

class MyArrayList<T> {
    T[] arr;

    @SafeVarargs
    @SuppressWarnings("varargs")
    MyArrayList(T... arr) {
        this.arr = arr;
    }

    @SafeVarargs
//	@SuppressWarnings("unchecked")
    public static <T> MyArrayList<T> asList(T... a) {
        return new MyArrayList<>(a);
    }

    public String toString() {
        return Arrays.toString(arr);
    }
}

class AnnotationEx4 {
    //	@SuppressWarnings("unchecked")
    public static void main(String args[]) {
        MyArrayList<String> list = MyArrayList.asList("1","2","3");

        System.out.println(list);
    }
}
```
```
[1, 2, 3]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
마지막으로, 애너테이션 타입 정의를 하는 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 12-13
```java
import java.lang.annotation.*;

@Deprecated
@SuppressWarnings("1111") // 유효하지 않은 애너테이션은 무시된다.
@TestInfo(testedBy="aaa", testDate=@DateTime(yymmdd="160101", hhmmss="235959"))
class AnnotationEx5 {
    public static void main(String args[]) {
        // AnnotaionEx5의 Class객체를 얻는다.
        Class<AnnotationEx5> cls = AnnotationEx5.class;

        TestInfo anno = (TestInfo)cls.getAnnotation(TestInfo.class);
        System.out.println("anno.testedBy()="+anno.testedBy());
        System.out.println("anno.testDate().yymmdd()="+anno.testDate().yymmdd());
        System.out.println("anno.testDate().hhmmss()="+anno.testDate().hhmmss());

        for(String str : anno.testTools())
            System.out.println("testTools="+str);

        System.out.println();

        // AnnotationEx5에 적용된 모든 애너테이션을 가져온다.
        Annotation[] annoArr = cls.getAnnotations();

        for(Annotation a : annoArr)
            System.out.println(a);
    } // main의 끝
}

@Retention(RetentionPolicy.RUNTIME)  // 실행 시에 사용가능하도록 지정
@interface TestInfo {
    int       count()	    default 1;
    String    testedBy();
    String[]  testTools()   default "JUnit";
    TestType  testType()    default TestType.FIRST;
    DateTime  testDate();
}

@Retention(RetentionPolicy.RUNTIME)  // 실행 시에 사용가능하도록 지정
@interface DateTime {
    String yymmdd();
    String hhmmss();
}

enum TestType { FIRST, FINAL }
```
```
anno.testedBy()=aaa
anno.testDate().yymmdd()=160101
anno.testDate().hhmmss()=235959
testTools=JUnit

@java.lang.Deprecated()
@TestInfo(count=1, testType=FIRST, testTools=[JUnit], testedBy=aaa, testDate=@DateTime(yymmdd=160101, hhmmss=235959))

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
이상으로 지네릭스, 열거형, 애너테이션에 대해 모두 알아보았습니다. 다음 포스팅에서는 입출력(I/O)에 대해 알아보겠습니다. 다음 포스팅을 끝으로 자바의 기초에 대한 공부 기록을 마무리하려고 합니다. 자바에 대한 포스팅이 모두 끝난 이후에는 스프링에 대한 공부 기록을 할 예정입니다. 스프링의 공부 범위는 최대한 기본 난이도의 선에서 기록하고자 합니다. 오늘도 긴 글 읽어주셔서 감사드리고 주말 잘 보내시길 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=