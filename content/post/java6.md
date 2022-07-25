---
title: "자바 공부 기록(6) - 객체지향 프로그래밍에 대해 알아볼까요?(2)"
date: 2022-07-25T11:41:56+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅은 저번에 이어서 객체지향 프로그래밍에 대해 알아보겠습니다. 시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 7장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다. 7장의 경우, 예제가 많이 작성되어 있으므로 예제를 정리하는 방향으로 포스팅을 작성할 예정입니다.
________________________________________________________________________________________________________________________________________________________________________
우선 상속부터 알아보겠습니다. 상속이란, 기존의 클래스를 재사용하여 새로운 클래스를 작성하는 것을 의미합니다. 상속을 통해서 클래스를 작성하면 보다 적은 양의 코드로 새로운 클래스를 작성할 수 있고 코드를 공통적으로 관리할 수 있기 때문에 코드의 추가 및 변경이 매우 용이합니다. 이러한 상속의 예제는 다음 4개와 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-1
```java
class Tv {
    boolean power; 	// 전원상태(on/off)
    int channel;	// 채널

    void power()        {   power = !power; }
    void channelUp()    { 	 ++channel;     }
    void channelDown()  {	 --channel;	    }
}

class CaptionTv extends Tv {
    boolean caption;		// 캡션상태(on/off)
    void displayCaption(String text) {
        if (caption) {	// 캡션상태가 on(true)일 때만 text를 보여준다.
            System.out.println(text);
        }
    }
}

class CaptionTvTest {
    public static void main(String args[]) {
        CaptionTv ctv = new CaptionTv();
        ctv.channel = 10;				// 조상 클래스로부터 상속받은 멤버
        ctv.channelUp();				// 조상 클래스로부터 상속받은 멤버
        System.out.println(ctv.channel);
        ctv.displayCaption("Hello, World");
        ctv.caption = true;				    // 캡션(자막) 기능을 켠다.
        ctv.displayCaption("Hello, World");
    }
}
```
```
11
Hello, World

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-2
```java
class DrawShape {
    public static void main(String[] args) {
        Point[] p = {   new Point(100, 100),
                new Point(140,  50),
                new Point(200, 100)
        };

        Triangle t = new Triangle(p);
        Circle   c = new Circle(new Point(150, 150), 50);

        t.draw(); // 삼각형을 그린다.
        c.draw(); // 원을 그린다.
    }
}

class Shape {
    String color = "black";
    void draw() {
        System.out.printf("[color=%s]%n", color);
    }
}

class Point {
    int x;
    int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    Point() {
        this(0,0);
    }

    String getXY() {
        return "("+x+","+y+")"; // x와 y의 값을 문자열로 반환
    }
}

class Circle extends Shape {
    Point center;	// 원의 원점좌표
    int r;			// 반지름

    Circle() {
        this(new Point(0, 0), 100); // Circle(Point center, int r)를 호출
    }

    Circle(Point center, int r) {
        this.center = center;
        this.r = r;
    }

    void draw() { // 원을 그리는 대신에 원의 정보를 출력하도록 했다.
        System.out.printf("[center=(%d, %d), r=%d, color=%s]%n", center.x, center.y, r, color);
    }
}

class Triangle extends Shape {
    Point[] p = new Point[3];

    Triangle(Point[] p) {
        this.p = p;
    }

    void draw() {
        System.out.printf("[p1=%s, p2=%s, p3=%s, color=%s]%n", p[0].getXY(), p[1].getXY(), p[2].getXY(), color);
    }
}
```
```
[p1=(100,100), p2=(140,50), p3=(200,100), color=black]
[center=(150, 150), r=50, color=black]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-3
```java
class DeckTest {
    public static void main(String args[]) {
        Deck d = new Deck();	   // 카드 한 벌(Deck)을 만든다.
        Card c = d.pick(0);	   // 섞기 전에 제일 위의 카드를 뽑는다.
        System.out.println(c); // System.out.println(c.toString());과 같다.

        d.shuffle();			   // 카드를 섞는다.
        c = d.pick(0);		   // 섞은 후에 제일 위의 카드를 뽑는다.
        System.out.println(c);
    }
}


class Deck {
    final int CARD_NUM = 52;	// 카드의 개수
    Card cardArr[] = new Card[CARD_NUM];  // Card객체 배열을 포함

    Deck () {	// Deck의 카드를 초기화한다.
        int i=0;

        for(int k=Card.KIND_MAX; k > 0; k--)
            for(int n=0; n < Card.NUM_MAX ; n++)
                cardArr[i++] = new Card(k, n+1);
    }

    Card pick(int index) {	
        return cardArr[index];
    }

    Card pick() {			// Deck에서 카드 하나를 선택한다.
        int index = (int)(Math.random() * CARD_NUM);
        return pick(index);
    }

    void shuffle() { // 카드의 순서를 섞는다.
        for(int i=0; i < cardArr.length; i++) {
            int r = (int)(Math.random() * CARD_NUM);

            Card temp = cardArr[i];
            cardArr[i] = cardArr[r];
            cardArr[r] = temp;
        }
    }
} // Deck클래스의 끝


class Card {
    static final int KIND_MAX = 4;	// 카드 무늬의 수
    static final int NUM_MAX  = 13;	// 무늬별 카드 수

    static final int SPADE   = 4;
    static final int DIAMOND = 3;
    static final int HEART   = 2;
    static final int CLOVER  = 1;

    int kind;
    int number;

    Card() {
        this(SPADE, 1);
    }

    Card(int kind, int number) {
        this.kind = kind;
        this.number = number;
    }

    public String toString() {
        String[] kinds = {"", "CLOVER", "HEART", "DIAMOND", "SPADE"};
        String numbers = "0123456789XJQK"; // 숫자 10은 X로 표현

        return "kind : " + kinds[this.kind]
                + ", number : " + numbers.charAt(this.number);
    } // toString()의 끝
} // Card클래스의 끝
```
```
kind : SPADE, number : 1
kind : HEART, number : 6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-4
```java
class Tv {
    boolean power; 	// 전원상태(on/off)
    int channel;		// 채널

    void power()       { 	power = !power; }
    void channelUp()   { 	++channel; }
    void channelDown() {	--channel; }
}

class VCR {
    boolean power; 	// 전원상태(on/off)
    int counter = 0;
    void power() { 	power = !power; }
    void play()  { /* 내용생략*/ }
    void stop()  { /* 내용생략*/ }
    void rew()   { /* 내용생략*/ }
    void ff()    { /* 내용생략*/ }
}

class TVCR extends Tv {
    VCR vcr = new VCR();
    int counter = vcr.counter;

    void play() {
        vcr.play();
    }

    void stop() {
        vcr.stop();
    }

    void rew() {
        vcr.rew();
    }

    void ff() {
        vcr.ff();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 오버라이딩에 대해 알아보겠습니다. 오버라이딩이란 조상 클래스로부터 상속받은 메서드의 내용을 변경하는 것을 의미합니다. 상속받은 메서드를 그대로 사용하기도 하지만, 자손 클래스 자신에 맞게 변경해야하는 경우가 많습니다. 이럴 때 조상의 메서드를 오버라이딩을 하면 됩니다. 오버라이딩에 대한 4개의 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-5
```java
class SuperTest {
    public static void main(String args[]) {
        Child c = new Child();
        c.method();
    }
}

class Parent {
    int x=10;
}

class Child extends Parent {
    void method() {
        System.out.println("x=" + x);
        System.out.println("this.x=" + this.x);
        System.out.println("super.x="+ super.x);
    }
}
```
```
x=10
this.x=10
super.x=10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-6
```java
class SuperTest2 {
    public static void main(String args[]) {
        Child c = new Child();
        c.method();
    }
}
class Parent {
    int x=10;
}

class Child extends Parent {
    int x=20;

    void method() {
        System.out.println("x=" + x);
        System.out.println("this.x=" + this.x);
        System.out.println("super.x="+ super.x);
    }
}
```
```
x=20
this.x=20
super.x=10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예제 7-7은 컴파일 에러가 발생하는 점 참고하시길 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-7
```java
class PointTest {
    public static void main(String args[]) {
        Point3D p3 = new Point3D(1,2,3);
    }
}

class Point {
    int x;
    int y;

    Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    String getLocation() {
        return "x :" + x + ", y :"+ y;
    }
}

class Point3D extends Point {
    int z;

    Point3D(int x, int y, int z) {

        this.x = x;
        this.y = y;
        this.z = z;
    }

    String getLocation() {	// 오버라이딩
        return "x :" + x + ", y :"+ y + ", z :" + z;
    }
}
```
```
java: constructor Point in class Point cannot be applied to given types;
  required: int,int
  found: no arguments
  reason: actual and formal argument lists differ in length
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-8
```java
class PointTest2 {
    public static void main(String argsp[]) {
        Point3D p3 = new Point3D();
        System.out.println("p3.x=" + p3.x);
        System.out.println("p3.y=" + p3.y);
        System.out.println("p3.z=" + p3.z);
    }
}

class Point {
    int x=10;
    int y=20;

    Point(int x, int y) {

        this.x = x;
        this.y = y;
    }
}

class Point3D extends Point {
    int z=30;

    Point3D() {
        this(100, 200, 300);	// Point3D(int x, int y, int z)를 호출한다.
    }

    Point3D(int x, int y, int z) {
        super(x, y);			// Point(int x, int y)를 호출한다.
        this.z = z;
    }
}
```
```
p3.x=100
p3.y=200
p3.z=300

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 package와 import에 대해 알아보겠습니다. 패키지란 클래스의 묶음이며, 임포트는 클래스의 패키지를 미리 명시해주는 역할을 합니다.
________________________________________________________________________________________________________________________________________________________________________
패키지를 실행하는 방법은 패키지를 생성하고 다음과 같은 방식으로 코드를 작성하면 됩니다. 참고로 맥 환경이므로 패키지 실행 방식이 윈도우와 다른 점 양해바랍니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-9
```java
package com.javachobo.book;

class PackageTest {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
그 다음 다음과 같이 IDE 내 터미널에서 명령어를 입력해주면 컴파일이 됩니다.
________________________________________________________________________________________________________________________________________________________________________
```
(base) shcDE-ui-MacBookPro:JavaVerse shc$ javac -d . src/PackageTest.java
(base) shcDE-ui-MacBookPro:JavaVerse shc$ 
```
________________________________________________________________________________________________________________________________________________________________________
코드 실행 결과, 정상적으로 출력됩니다.
________________________________________________________________________________________________________________________________________________________________________
```
Hello World!

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
import문에 대한 두가지 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-10
```java
import java.text.SimpleDateFormat;
import java.util.Date;

class ImportTest
{
    public static void main(String[] args)
    {
        Date today = new Date();

        SimpleDateFormat date = new SimpleDateFormat("yyyy/MM/dd");
        SimpleDateFormat time = new SimpleDateFormat("hh:mm:ss a");

        System.out.println("오늘 날짜는 " + date.format(today));
        System.out.println("현재 시간은 " + time.format(today));
    }
}
```
```
오늘 날짜는 2022/07/25
현재 시간은 12:43:03 PM

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-11
```java
import static java.lang.System.out;
import static java.lang.Math.*;

class StaticImportEx1 {
    public static void main(String[] args) {
        // System.out.println(Math.random());
        out.println(random());

        // System.out.println("Math.PI :"+Math.PI);
        out.println("Math.PI :" + PI);
    }
}
```
```
0.5983419396202335
Math.PI :3.141592653589793

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 제어자에 대해 알아보겠습니다. 제어자는 클래스, 변수 또는 메서드의 선언부에 함께 사용되어 부가적인 의미를 부여하는 역할을 합니다. 제어자의 종류는 크게 접근 제어자와 그 외의 제어자로 나눌 수 있습니다. 제어자와 관련된 예시는 다음 3개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-12
```java
class Card {
    final int NUMBER;		// 상수지만 선언과 함께 초기화 하지 않고
    final String KIND;		// 생성자에서 단 한번만 초기화할 수 있다.
    static int width  = 100;
    static int height = 250;

    Card(String kind, int num) {
        KIND = kind;
        NUMBER = num;
    }

    Card() {
        this("HEART", 1);
    }

    public String toString() {
        return KIND +" "+ NUMBER;
    }
}

class FinalCardTest {
    public static void main(String args[]) {
        Card c = new Card("HEART", 10);
//		c.NUMBER = 5;
        System.out.println(c.KIND);
        System.out.println(c.NUMBER);
        System.out.println(c); // System.out.println(c.toString());
    }
}
```
```
HEART
10
HEART 10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-13
```java
public class HelloWorldPrint {
    public static void main(String[] args)
    {
        Time t = new Time(12, 35, 30);
        System.out.println(t);
//          t.hour = 13;
        t.setHour(t.getHour()+1);   // 현재시간보다 1시간 후로 변경한다.
        System.out.println(t);      // System.out.println(t.toString());과 같다.
    }
}

class Time {
    private int hour;
    private int minute;
    private int second;

    Time(int hour, int minute, int second) {
        setHour(hour);
        setMinute(minute);
        setSecond(second);
    }

    public int getHour() { return hour; }
    public void setHour(int hour) {
        if (hour < 0 || hour > 23) return;
        this.hour = hour;
    }
    public int getMinute() { return minute; }
    public void setMinute(int minute) {
        if (minute < 0 || minute > 59) return;
        this.minute = minute;
    }
    public int getSecond() { return second; }
    public void setSecond(int second) {
        if (second < 0 || second > 59) return;
        this.second = second;
    }
    public String toString() {
        return hour + ":" + minute + ":" + second;
    }
} 
```
```
12:35:30
13:35:30

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-14
```java
final class Singleton {
    private static Singleton s = new Singleton();

    private Singleton() {
        //...
    }

    public static Singleton getInstance() {
        if(s==null) {
            s = new Singleton();
        }
        return s;
    }

    //...
}

class SingletonTest {
    public static void main(String args[]) {
		Singleton s = new Singleton();
        Singleton s = Singleton.getInstance();
    }
}
```
```
java: Singleton() has private access in Singleton
```
________________________________________________________________________________________________________________________________________________________________________
예제 7-14는 위와 같은 에러를 발생시킵니다.
________________________________________________________________________________________________________________________________________________________________________
다음으로 다형성에 대해 알아보겠습니다. 다형성이란 '여러 가지 형태를 가질 수 있는 능력'을 의미하며, 자바에서는 한 타입의 참조변수로 여러 타입의 객체를 참조할 수 있도록 함으로써 다형성을 프로그램적으로 구현하였습니다.
________________________________________________________________________________________________________________________________________________________________________
다형성에 대한 예제는 총 9개로, 출력 결과는 아래와 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-15
```java
class CastingTest1 {
    public static void main(String args[]) {
        Car car = null;
        FireEngine fe = new FireEngine();
        FireEngine fe2 = null;

        fe.water();
        car = fe;    // car =(Car)fe;에서 형변환이 생략된 형태다.
//		car.water();
        fe2 = (FireEngine)car; // 자손타입 <- 조상타입
        fe2.water();
    }
}

class Car {
    String color;
    int door;

    void drive() { 		// 운전하는 기능
        System.out.println("drive, Brrrr~");
    }

    void stop() {		// 멈추는 기능
        System.out.println("stop!!!");
    }
}

class FireEngine extends Car {	// 소방차
    void water() {		// 물을 뿌리는 기능
        System.out.println("water!!!");
    }
}
```
```
water!!!
water!!!

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-16
```java
class CastingTest2 {
    public static void main(String args[]) {
        Car car = new Car();
        Car car2 = null;
        FireEngine fe = null;

        car.drive();
        fe = (FireEngine)car;		// 8번째 줄. 컴파일은 OK. 실행 시 에러가 발생
        fe.drive();
        car2 = fe;
        car2.drive();
    }
}
```
```
java: cannot find symbol
  symbol:   class Car
  location: class CastingTest2
```
________________________________________________________________________________________________________________________________________________________________________
예제 7-16에서는 다음과 같이 에러가 발생합니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-17
```java
class InstanceofTest {
    public static void main(String args[]) {
        FireEngine fe = new FireEngine();

        if(fe instanceof FireEngine) {
            System.out.println("This is a FireEngine instance.");
        }

        if(fe instanceof Car) {
            System.out.println("This is a Car instance.");
        }

        if(fe instanceof Object) {
            System.out.println("This is an Object instance.");
        }

        System.out.println(fe.getClass().getName()); // 클래스의 이름을 출력
    }
} // class
class Car {}
class FireEngine extends Car {}
```
```
This is a FireEngine instance.
This is a Car instance.
This is an Object instance.
FireEngine

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-18
```java
class BindingTest{
    public static void main(String[] args) {
        Parent p = new Child();
        Child c = new Child();

        System.out.println("p.x = " + p.x);
        p.method();

        System.out.println("c.x = " + c.x);
        c.method();
    }
}

class Parent {
    int x = 100;

    void method() {
        System.out.println("Parent Method");
    }
}

class Child extends Parent {
    int x = 200;

    void method() {
        System.out.println("Child Method");
    }
}
```
```
p.x = 100
Child Method
c.x = 200
Child Method

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-19
```java
class BindingTest2 {
    public static void main(String[] args) {
        Parent p = new Child();
        Child c = new Child();

        System.out.println("p.x = " + p.x);
        p.method();

        System.out.println("c.x = " + c.x);
        c.method();
    }
}

class Parent {
    int x = 100;

    void method() {
        System.out.println("Parent Method");
    }
}

class Child extends Parent { }
```
```
p.x = 100
Parent Method
c.x = 100
Parent Method

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-20
```java
class BindingTest3{
    public static void main(String[] args) {
        Parent p = new Child();
        Child  c = new Child();

        System.out.println("p.x = " + p.x);
        p.method();
        System.out.println();
        System.out.println("c.x = " + c.x);
        c.method();
    }
}

class Parent {
    int x = 100;

    void method() {
        System.out.println("Parent Method");
    }
}

class Child extends Parent {
    int x = 200;

    void method() {
        System.out.println("x=" + x);  // this.x와 같다.
        System.out.println("super.x=" + super.x);
        System.out.println("this.x="  + this.x);
    }
}
```
```
p.x = 100
x=200
super.x=100
this.x=200

c.x = 200
x=200
super.x=100
this.x=200

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-21
```java
class Product {
    int price;			// 제품의 가격
    int bonusPoint;		// 제품구매 시 제공하는 보너스점수

    Product(int price) {
        this.price = price;
        bonusPoint =(int)(price/10.0);	// 보너스점수는 제품가격의 10%
    }
}

class Tv extends Product {
    Tv() {
        // 조상클래스의 생성자 Product(int price)를 호출한다.
        super(100);			// Tv의 가격을 100만원으로 한다.
    }

    public String toString() {
        return "Tv";
    }
}

class Computer extends Product {
    Computer() {
        super(200);
    }

    public String toString() {
        return "Computer";
    }
}

class Buyer {			// 고객, 물건을 사는 사람
    int money = 1000;	// 소유금액
    int bonusPoint = 0;	// 보너스점수

    void buy(Product p) {
        if(money < p.price) {
            System.out.println("잔액이 부족하여 물건을 살 수 없습니다.");
            return;
        }

        money -= p.price;			// 가진 돈에서 구입한 제품의 가격을 뺀다.
        bonusPoint += p.bonusPoint;	// 제품의 보너스 점수를 추가한다.
        System.out.println(p + "을/를 구입하셨습니다.");
    }
}

class PolyArgumentTest {
    public static void main(String args[]) {
        Buyer b = new Buyer();

        b.buy(new Tv());
        b.buy(new Computer());

        System.out.println("현재 남은 돈은 " + b.money + "만원입니다.");
        System.out.println("현재 보너스점수는 " + b.bonusPoint + "점입니다.");
    }
}
```
```
Tv을/를 구입하셨습니다.
Computer을/를 구입하셨습니다.
현재 남은 돈은 700만원입니다.
현재 보너스점수는 30점입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-22
```java
class Product {
    int price;			// 제품의 가격
    int bonusPoint;		// 제품구매 시 제공하는 보너스점수

    Product(int price) {
        this.price = price;
        bonusPoint =(int)(price/10.0);
    }

    Product() {} // 기본 생성자
}

class Tv extends Product {
    Tv() {
        super(100);
    }

    public String toString() { return "Tv"; }
}

class Computer extends Product {
    Computer() { super(200); }

    public String toString() { return "Computer"; }
}

class Audio extends Product {
    Audio() { super(50); }

    public String toString() { return "Audio"; }
}

class Buyer {			// 고객, 물건을 사는 사람
    int money = 1000;	// 소유금액
    int bonusPoint = 0;	// 보너스점수
    Product[] item = new Product[10];	// 구입한 제품을 저장하기 위한 배열
    int i =0;			// Product배열에 사용될 카운터

    void buy(Product p) {
        if(money < p.price) {
            System.out.println("잔액이 부족하여 물건을 살 수 없습니다.");
            return;
        }

        money -= p.price;	        // 가진 돈에서 구입한 제품의 가격을 뺀다.
        bonusPoint += p.bonusPoint;	// 제품의 보너스 점수를 추가한다.
        item[i++] = p;		        // 제품을 Product[] item에 저장한다.
        System.out.println(p + "을/를 구입하셨습니다.");
    }

    void summary() {		    // 구매한 물품에 대한 정보를 요약해서 보여 준다.
        int sum = 0;		 	// 구입한 물품의 가격합계
        String itemList =""; 	// 구입한 물품목록

        // 반복문을 이용해서 구입한 물품의 총 가격과 목록을 만든다.
        for(int i=0; i<item.length;i++) {
            if(item[i]==null) break;
            sum += item[i].price;
            itemList += item[i] + ", ";
        }
        System.out.println("구입하신 물품의 총금액은 " + sum + "만원입니다.");
        System.out.println("구입하신 제품은 " + itemList + "입니다.");
    }
}

class PolyArgumentTest2 {
    public static void main(String args[]) {
        Buyer b = new Buyer();

        b.buy(new Tv());
        b.buy(new Computer());
        b.buy(new Audio());
        b.summary();
    }
}
```
```
Tv을/를 구입하셨습니다.
Computer을/를 구입하셨습니다.
Audio을/를 구입하셨습니다.
구입하신 물품의 총금액은 350만원입니다.
구입하신 제품은 Tv, Computer, Audio, 입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-23
```java
import java.util.*;			// Vector클래스를 사용하기 위해서 추가

class Product {
    int price;			// 제품의 가격
    int bonusPoint;		// 제품구매 시 제공하는 보너스점수

    Product(int price) {
        this.price = price;
        bonusPoint =(int)(price/10.0);
    }

    Product() {
        price = 0;
        bonusPoint = 0;
    }
}

class Tv extends Product {
    Tv() { super(100); }
    public String toString() { return "Tv"; }
}

class Computer extends Product {
    Computer() { super(200); }
    public String toString() { return "Computer"; }
}

class Audio extends Product {
    Audio() { super(50); }
    public String toString() { return "Audio"; }
}

class Buyer {			// 고객, 물건을 사는 사람
    int money = 1000;  	// 소유금액
    int bonusPoint = 0;	// 보너스점수
    Vector item = new Vector();	// 구입한 제품을 저장하는데 사용될 Vector객체

    void buy(Product p) {
        if(money < p.price) {
            System.out.println("잔액이 부족하여 물건을 살 수 없습니다.");
            return;
        }
        money -= p.price;			// 가진 돈에서 구입한 제품의 가격을 뺀다.
        bonusPoint += p.bonusPoint;	// 제품의 보너스 점수를 추가한다.
        item.add(p);				// 구입한 제품을 Vector에 저장한다.
        System.out.println(p + "을/를 구입하셨습니다.");
    }

    void refund(Product p) {	// 구입한 제품을 환불한다.
        if(item.remove(p)) {	// 제품을 Vector에서 제거한다.
            money += p.price;
            bonusPoint -= p.bonusPoint;
            System.out.println(p + "을/를 반품하셨습니다.");
        } else {			//  제거에 실패한 경우
            System.out.println("구입하신 제품 중 해당 제품이 없습니다.");
        }
    }

    void summary() {		      // 구매한 물품에 대한 정보를 요약해서 보여준다.
        int sum = 0;		      // 구입한 물품의 가격합계
        String itemList =""; 	  // 구입한 물품목록

        if(item.isEmpty()) {	// Vector가 비어있는지 확인한다.
            System.out.println("구입하신 제품이 없습니다.");
            return;
        }

        // 반복문을 이용해서 구입한 물품의 총 가격과 목록을 만든다.
        for(int i=0; i<item.size();i++) {
            Product p = (Product)item.get(i);
            sum += p.price;
            itemList += (i==0) ? "" + p : ", " + p;
        }
        System.out.println("구입하신 물품의 총금액은 " + sum + "만원입니다.");
        System.out.println("구입하신 제품은 " + itemList + "입니다.");
    }
}

class PolyArgumentTest3 {
    public static void main(String args[]) {
        Buyer b = new Buyer();
        Tv tv = new Tv();
        Computer com = new Computer();
        Audio audio = new Audio();

        b.buy(tv);
        b.buy(com);
        b.buy(audio);
        b.summary();
        System.out.println();
        b.refund(com);
        b.summary();
    }
}
```
```
Tv을/를 구입하셨습니다.
Computer을/를 구입하셨습니다.
Audio을/를 구입하셨습니다.
구입하신 물품의 총금액은 350만원입니다.
구입하신 제품은 Tv, Computer, Audio입니다.

Computer을/를 반품하셨습니다.
구입하신 물품의 총금액은 150만원입니다.
구입하신 제품은 Tv, Audio입니다.
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 인터페이스에 대해 알아보겠습니다. 인터페이스는 일종의 추상클래스입니다. 하지만 인터페이스는 추상클래스처럼 추상메서드를 갖지만 추상클래스보다 추상화 정도가 높아서 추상클래스와 달리 몸통을 갖춘 일반 메서드 또는 멤버변수를 구성원으로 가질 수 없습니다. 오직 추상메서드와 상수만을 멤버로 가질 수 있으며, 그 외의 다른 어떠한 요소도 허용하지 않습니다. 인터페이스에 대한 예제는 다음과 같이 총 7개로 이루어져 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-24
```java
class FighterTest {
    public static void main(String[] args) {
        Fighter f = new Fighter();

        if (f instanceof Unit)	{
            System.out.println("f는 Unit클래스의 자손입니다.");
        }
        if (f instanceof Fightable) {
            System.out.println("f는 Fightable인터페이스를 구현했습니다.");
        }
        if (f instanceof Movable) {
            System.out.println("f는 Movable인터페이스를 구현했습니다.");
        }
        if (f instanceof Attackable) {
            System.out.println("f는 Attackable인터페이스를 구현했습니다.");
        }
        if (f instanceof Object) {
            System.out.println("f는 Object클래스의 자손입니다.");
        }
    }
}

class Fighter extends Unit implements Fightable {
    public void move(int x, int y) { /* 내용 생략 */ }
    public void attack(Unit u) { /* 내용 생략 */ }
}

class Unit {
    int currentHP;	// 유닛의 체력
    int x;			// 유닛의 위치(x좌표)
    int y;			// 유닛의 위치(y좌표)
}

interface Fightable extends Movable, Attackable { }
interface Movable {	void move(int x, int y);	}
interface Attackable {	void attack(Unit u); }
```
```
f는 Unit클래스의 자손입니다.
f는 Fightable인터페이스를 구현했습니다.
f는 Movable인터페이스를 구현했습니다.
f는 Attackable인터페이스를 구현했습니다.
f는 Object클래스의 자손입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-25
```java
interface Parseable {
    // 구문 분석작업을 수행한다.
    public abstract void parse(String fileName);
}

class ParserManager {
    // 리턴타입이 Parseable 인터페이스이다.
    public static Parseable getParser(String type) {
        if(type.equals("XML")) {
            return new XMLParser();
        } else {
            Parseable p = new HTMLParser();
            return p;
            // return new HTMLParser();
        }
    }
}

class XMLParser implements Parseable {
    public void parse(String fileName) {
        /* 구문 분석작업을 수행하는 코드를 적는다. */
        System.out.println(fileName + "- XML parsing completed.");
    }
}

class HTMLParser implements Parseable {
    public void parse(String fileName) {
        /* 구문 분석작업을 수행하는 코드를 적는다. */
        System.out.println(fileName + "-HTML parsing completed.");
    }
}

class ParserTest {
    public static void main(String args[]) {
        Parseable parser = ParserManager.getParser("XML");
        parser.parse("document.xml");
        parser = ParserManager.getParser("HTML");
        parser.parse("document2.html");
    }
}
```
```
document.xml- XML parsing completed.
document2.html-HTML parsing completed.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-26
```java
class RepairableTest{
    public static void main(String[] args) {
        Tank tank = new Tank();
        Dropship dropship = new Dropship();

        Marine marine = new Marine();
        SCV	scv = new SCV();

        scv.repair(tank);	// SCV가 Tank를 수리하도록 한다.
        scv.repair(dropship);
//		scv.repair(marine);
    }
}

interface Repairable {}
class GroundUnit extends Unit {
    GroundUnit(int hp) {
        super(hp);
    }
}

class AirUnit extends Unit {
    AirUnit(int hp) {
        super(hp);
    }
}

class Unit {
    int hitPoint;
    final int MAX_HP;
    Unit(int hp) {
        MAX_HP = hp;
    }
    //...
}

class Tank extends GroundUnit implements Repairable {
    Tank() {
        super(150);		// Tank의 HP는 150이다.
        hitPoint = MAX_HP;
    }

    public String toString() {
        return "Tank";
    }
    //...
}

class Dropship extends AirUnit implements Repairable {
    Dropship() {
        super(125);		// Dropship의 HP는 125이다.
        hitPoint = MAX_HP;
    }

    public String toString() {
        return "Dropship";
    }
    //...
}

class Marine extends GroundUnit {
    Marine() {
        super(40);
        hitPoint = MAX_HP;
    }
    //...
}

class SCV extends GroundUnit implements Repairable{
    SCV() {
        super(60);
        hitPoint = MAX_HP;
    }

    void repair(Repairable r) {
        if (r instanceof Unit) {
            Unit u = (Unit)r;
            while(u.hitPoint!=u.MAX_HP) {
                /* Unit의 HP를 증가시킨다. */
                u.hitPoint++;
            }
            System.out.println( u.toString() + "의 수리가 끝났습니다.");
        }
    }
    //...
}
```
```
Tank의 수리가 끝났습니다.
Dropship의 수리가 끝났습니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-27
```java
class A {
    public void methodA(B b) {
        b.methodB();
    }
}

class B {
    public void methodB() {
        System.out.println("methodB()");
    }
}

class InterfaceTest {
    public static void main(String args[]) {
        A a = new A();
        a.methodA(new B());
    }
}
```
```
methodB()

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-28
```java
class A {
    void autoPlay(I i) {
        i.play();
    }
}

interface I {
    public abstract void play();
}

class B implements I {
    public void play() {
        System.out.println("play in B class");
    }
}

class C implements I {
    public void play() {
        System.out.println("play in C class");
    }
}

class InterfaceTest2 {
    public static void main(String[] args) {
        A a = new A();
        a.autoPlay(new B()); // void autoPlay(I i)호출
        a.autoPlay(new C()); // void autoPlay(I i)호출
    }
}
```
```
play in B class
play in C class

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-29
```java
class InterfaceTest3 {
	public static void main(String[] args) {
		A a = new A();
		a.methodA();
	}
}

 class A {
    void methodA() {
          I i = InstanceManager.getInstance();
		  i.methodB();
		  System.out.println(i.toString()); // i로 Object클래스의 메서드 호출가능
     }
 }

 interface I {
      public abstract void methodB();
 }

 class B implements I {
     public void methodB() {
          System.out.println("methodB in B class");
     }

	  public String toString() { return "class B";}
 }

 class InstanceManager {
	public static I getInstance() {
		return new B();
	}
 }
```
```
methodB in B class
class B

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-30
```java
class DefaultMethodTest {
    public static void main(String[] args) {
        Child c = new Child();
        c.method1();
        c.method2();
        MyInterface.staticMethod();
        MyInterface2.staticMethod();
    }
}

class Child extends Parent implements MyInterface, MyInterface2 {
    public void method1() {
        System.out.println("method1() in Child"); // 오버라이딩
    }
}

class Parent {
    public void method2() {
        System.out.println("method2() in Parent");
    }
}

interface MyInterface {
    default void method1() {
        System.out.println("method1() in MyInterface");
    }

    default void method2() {
        System.out.println("method2() in MyInterface");
    }

    static  void staticMethod() {
        System.out.println("staticMethod() in MyInterface");
    }
}

interface MyInterface2 {
    default void method1() {
        System.out.println("method1() in MyInterface2");
    }

    static  void staticMethod() {
        System.out.println("staticMethod() in MyInterface2");
    }
}
```
```
method1() in Child
method2() in Parent
staticMethod() in MyInterface
staticMethod() in MyInterface2

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 내부 클래스에 대해 알아보겠습니다. 내부 클래스란 클래스 내에 선언된 클래스입니다. 클래스에 다른 클래스를 선언하는 이유는 간단한데, 두 클래스가 서로 긴밀한 관계에 있기 때문입니다. 이러한 내부 클래스에 관련된 예제는 총 8개이며 아래와 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-31
```java
class InnerEx1 {
    class InstanceInner {
        int iv = 100;
        //          static int cv = 100;            // 에러! static변수를 선언할 수 없다.
        final static int CONST = 100;   // final static은 상수이므로 허용
    }

    static class StaticInner {
        int iv = 200;
        static int cv = 200;       // static클래스만 static멤버를 정의할 수 있다.
    }

    void myMethod() {
        class LocalInner {
            int iv = 300;
            //                static int cv = 300;          // 에러! static변수를 선언할 수 없다.
            final static int CONST = 300; // final static은 상수이므로 허용
        }
    }

    public static void main(String args[]) {
        System.out.println(InstanceInner.CONST);
        System.out.println(StaticInner.cv);
    }
} 
```
```
100
200

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-32
```java
class InnerEx2 {
    class InstanceInner {}
    static class StaticInner {}

    // 인스턴스 멤버 간에는 서로 직접 접근이 가능하다.
    InstanceInner iv = new InstanceInner();
    // static 멤버 간에는 서로 직접 접근이 가능하다.
    static StaticInner cv = new StaticInner();

    static void staticMethod() {
        // static멤버는 인스턴스멤버에 직접 접근할 수 없다.
//		InstanceInner obj1 = new InstanceInner();	
        StaticInner obj2 = new StaticInner();

        //	굳이 접근하려면 아래와 같이 객체를 생성해야 한다.
        //	인스턴스 클래스는 외부 클래스를 먼저 생성해야만 생성할 수 있다.
        InnerEx2 outer = new InnerEx2();
        InstanceInner obj1 = outer.new InstanceInner();
    }

    void instanceMethod() {
        // 인스턴스 메서드에서는 인스턴스 멤버와 static멤버 모두 접근 가능하다.
        InstanceInner obj1 = new InstanceInner();
        StaticInner obj2 = new StaticInner();
        // 메서드 내에 지역적으로 선언된 내부 클래스는 외부에서 접근할 수 없다.
//		LocalInner lv = new LocalInner();
    }

    void myMethod() {
        class LocalInner {}
        LocalInner lv = new LocalInner();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-33
```java
class InnerEx3 {
    private int outerIv = 0;
    static int outerCv = 0;

    class InstanceInner {
        int iiv  = outerIv;  // 외부 클래스의 private멤버도 접근가능하다.
        int iiv2 = outerCv;
    }

    static class StaticInner {
        // 스태틱 클래스는 외부 클래스의 인스턴스멤버에 접근할 수 없다.
//		int siv = outerIv;
        static int scv = outerCv;
    }

    void myMethod() {
        int lv = 0;
        final int LV = 0;  // JDK1.8부터 final 생략 가능

        class LocalInner {
            int liv = outerIv;
            int liv2 = outerCv;
            //	외부 클래스의 지역변수는 final이 붙은 변수(상수)만 접근가능하다.
            int liv3 = lv;	// 에러!!!(JDK1.8부터 에러 아님)
            int liv4 = LV;	// OK
        }
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-34
```java
class Outer {
    class InstanceInner {
        int iv=100;
    }
    static class StaticInner {
        int iv=200;
        static int cv=300;
    }

    void myMethod() {
        class LocalInner {
            int iv=400;
        }
    }
}

class InnerEx4 {
    public static void main(String[] args) {
        // 인스턴스클래스의 인스턴스를 생성하려면
        // 외부 클래스의 인스턴스를 먼저 생성해야 한다.
        Outer oc = new Outer();
        Outer.InstanceInner ii = oc.new InstanceInner();

        System.out.println("ii.iv : "+ ii.iv);
        System.out.println("Outer.StaticInner.cv : " + Outer.StaticInner.cv);

        // 스태틱 내부 클래스의 인스턴스는 외부 클래스를 먼저 생성하지 않아도 된다.
        Outer.StaticInner si = new Outer.StaticInner();
        System.out.println("si.iv : "+ si.iv);
    }
}
```
```
ii.iv : 100
Outer.StaticInner.cv : 300
si.iv : 200

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-35
```java
class Outer {
    int value=10;	// Outer.this.value

    class Inner {
        int value=20;	// this.value

        void method1() {
            int value=30;
            System.out.println("           value :" + value);
            System.out.println("      this.value :" + this.value);
            System.out.println("Outer.this.value :" + Outer.this.value);
        }
    } // Inner클래스의 끝
} // Outer클래스의 끝

class InnerEx5 {
    public static void main(String args[]) {
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();
        inner.method1();
    }
} // InnerEx5 끝
```
```
           value :30
      this.value :20
Outer.this.value :10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-36
```java
class InnerEx6 {
    Object iv = new Object(){ void method(){} };		// 익명 클래스
    static Object cv = new Object(){ void method(){} };	// 익명 클래스

    void myMethod() {
        Object lv = new Object(){ void method(){} };	// 익명 클래스
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-37
```java
import java.awt.*;
import java.awt.event.*;

class  InnerEx7{
	public static void main(String[] args) {
		Button b = new Button("Start");
		b.addActionListener(new EventHandler());
	}
}

class EventHandler implements ActionListener {
	public void actionPerformed(ActionEvent e) {
		System.out.println("ActionEvent occurred!!!");
	}
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 7-38
```java
import java.awt.*;
import java.awt.event.*;

class  InnerEx8 {
    public static void main(String[] args) {
        Button b = new Button("Start");
        b.addActionListener(new ActionListener() {
                                public void actionPerformed(ActionEvent e) {
                                    System.out.println("ActionEvent occurred!!!");
                                }
                            } // 익명 클래스의 끝
        );
    } // main의 끝
} // InnerEx8의 끝
```
```

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
이렇게 두개의 포스팅에 걸쳐, 객체지향 프로그래밍에 대해 확인했습니다. 예제가 무려 70 여개나 되어 지칠 수도 있습니다. 하지만 꾸준히 포기하지 않고 공부하시면 객체지향에 대한 기초를 다지실 수 있을 것입니다. 이번 포스팅은 여기에서 마치고 '예외처리'에 관한 포스팅으로 돌아오겠습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=