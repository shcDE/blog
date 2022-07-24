---
title: "자바 공부 기록(5) - 객체지향 프로그래밍에 대해 알아볼까요?(1)"
date: 2022-07-24T15:30:43+09:00
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
# 자바에서 객체지향 프로그래밍은 어떻게 할까요?
________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅부터는 객체지향 프로그래밍에 대해 2~3회에 걸쳐 설명하겠습니다. 여기에서 말하는 객체지향언어란 기존의 프로그래밍 언어와 다른 전혀 새로운 것이 아니라, 기존의 프로그래밍 언어에 몇가지 새로운 규칙을 추가한 보다 발전된 형태의 언어입니다. 이러한 규칙들을 이용해서 코드 간에 서로 관계를 맺어줌으로써 보다 유기적으로 프로그램을 구성하는 것이 가능해졌습니다. 기존의 프로그래밍 언어에 익숙한 사람이라면 자바의 객체지향적인 부분만 새로 배우면 됩니다. 다만 절차적 언어에 익숙한 프로그래밍 습관을 객체지향적으로 바꾸도록 노력을 해야 할 것입니다. 이러한 객체지향언어의 주요 특징은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
1. 코드의 재사용성이 높다.
2. 코드의 관리가 용이하다.
3. 신뢰성이 높은 프로그래밍을 가능하게 한다.
________________________________________________________________________________________________________________________________________________________________________
이러한 세가지의 주요 장점을 활용하기 위해 지금부터 객체지향언어에 대해 다양한 예시와 함께 살펴보겠습니다. 
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 6장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
________________________________________________________________________________________________________________________________________________________________________
우선 인스턴스의 생성과 사용에 대해 알아보겠습니다. 해당 예시는 Tv클래스로부터 인스턴스를 생성하고 인스턴스의 속성(channel)과 메서드(channelDown())를 사용하는 방법을 보여줍니다. 해당 예시에서 볼 수 있듯이, IDE에 모든 코드를 삭제하고 클래스를 명시하는 코드를 작성해도 정상적으로 코드가 실행된다는 점 참고해주시면 될 것 같습니다.
_______________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-1
```java
class Tv {
    // Tv의 속성
    String color;           	// 색상
    boolean power;         	// 전원상태(on/off)
    int channel;           	// 채널

    // Tv의 기능(메서드)
    void power()   { power = !power; }  // TV를 켜거나 끄는 기능을 하는 메서드
    void channelUp()   {  ++channel; }  // TV의 채널을 높이는 기능을 하는 메서드
    void channelDown() { --channel; }   // TV의 채널을 낮추는 기능을 하는 메서드
}

class TvTest {
    public static void main(String args[]) {
        Tv t;                  // Tv인스턴스를 참조하기 위한 변수 t를 선언
        t = new Tv();          // Tv인스턴스를 생성한다.
        t.channel = 7;         // Tv인스턴스의 멤버변수 channel의 값을 7로 한다.
        t.channelDown();       // Tv인스턴스의 메서드 channelDown()을 호출한다.
        System.out.println("현재 채널은 " + t.channel + " 입니다.");
    }
}
```
```
현재 채널은 6 입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예제 6-1을 응용한 사례는 다음 두개의 예제와 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-2
```java
class Tv {
    // Tv의 속성(멤버변수)
    String color;          // 색상
    boolean power;         // 전원상태(on/off)
    int channel;           // 채널

    // Tv의 기능(메서드)
    void power()   { power = !power; }  // TV를 켜거나 끄는 기능을 하는 메서드
    void channelUp()   {  ++channel; }  // TV의 채널을 높이는 기능을 하는 메서드
    void channelDown() {  --channel; }  // TV의 채널을 낮추는 기능을 하는 메서드
}

class TvTest2 {
    public static void main(String args[]) {
        Tv t1 = new Tv();  // Tv t1; t1 = new Tv();를 한 문장으로 가능
        Tv t2 = new Tv();
        System.out.println("t1의 channel1값은 " + t1.channel + "입니다.");
        System.out.println("t2의 channel1값은 " + t2.channel + "입니다.");

        t1.channel = 7;	// channel 값을 7로 한다.
        System.out.println("t1의 channel값을 7로 변경하였습니다.");

        System.out.println("t1의 channel값은 " + t1.channel + "입니다.");
        System.out.println("t2의 channel값은 " + t2.channel + "입니다.");
    }
}
```
```
t1의 channel1값은 0입니다.
t2의 channel1값은 0입니다.
t1의 channel값을 7로 변경하였습니다.
t1의 channel값은 7입니다.
t2의 channel값은 0입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-3
```java
class Tv {
    // Tv의 속성(멤버변수)
    String color;           // 색상
    boolean power;          // 전원상태(on/off)
    int channel;          	 // 채널

    // Tv의 기능(메서드)
    void power()   { power = !power; }  // TV를 켜거나 끄는 기능을 하는 메서드
    void channelUp()   {  ++channel; }  // TV의 채널을 높이는 기능을 하는 메서드
    void channelDown() {  --channel; }  // TV의 채널을 낮추는 기능을 하는 메서드
}

class TvTest3 {
    public static void main(String args[]) {
        Tv t1 = new Tv();
        Tv t2 = new Tv();
        System.out.println("t1의 channel값은 " + t1.channel + "입니다.");
        System.out.println("t2의 channel값은 " + t2.channel + "입니다.");

        t2 = t1;		// t1이 저장하고 있는 값(주소)을 t2에 저장한다.
        t1.channel = 7;	// channel 값을 7로 한다.
        System.out.println("t1의 channel값을 7로 변경하였습니다.");

        System.out.println("t1의 channel값은 " + t1.channel + "입니다.");
        System.out.println("t2의 channel값은 " + t2.channel + "입니다.");
    }
}
```
```
t1의 channel값은 0입니다.
t2의 channel값은 0입니다.
t1의 channel값을 7로 변경하였습니다.
t1의 channel값은 7입니다.
t2의 channel값은 7입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
객체 배열은 활용한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-4
```java
class TvTest4 {
    public static void main(String args[]) {
        Tv[] tvArr = new Tv[3]; // 길이가 3인 Tv객체 배열

        // Tv객체를 생성해서 Tv객체 배열의 각 요소에 저장
        for(int i=0; i < tvArr.length;i++) {
            tvArr[i] = new Tv();
            tvArr[i].channel = i+10; // tvArr[i]의 channel에 i+10을 저장
        }

        for(int i=0; i < tvArr.length;i++) {
            tvArr[i].channelUp();  // tvArr[i]의 메서드를 호출, 채널이 1증가
            System.out.printf("tvArr[%d].channel=%d%n",i,tvArr[i].channel);
        }
    } // main의 끝
}

class Tv {
    String color;           // 색상
    boolean power;          // 전원상태(on/off)
    int channel;          	 // 채널

    void power()   { power = !power; }
    void channelUp()   {  ++channel; }
    void channelDown() {  --channel; }
}
```
```
tvArr[0].channel=11
tvArr[1].channel=12
tvArr[2].channel=13

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음 예제는 클래스 변수와 인스턴스 변수를 비교하는 사례를 보여줍니다. 코드를 보여주기 전에, 각 변수에 대해 간단히 설명드리겠습니다.  
인스턴스 변수는 클래스 영역에 선언되며, 클래스의 인스턴스를 생성할 때 만들어집니다.  
클래스 변수는 인스턴스 변수 앞에 static이 붙어 있다는 점에서 차이를 보입니다.  
그러면 각 변수가 어떻게 호출되는지 확인하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-5
```java
class CardTest{
    public static void main(String args[]) {
        System.out.println("Card.width = "  + Card.width);
        System.out.println("Card.height = " + Card.height);

        Card c1 = new Card();
        c1.kind = "Heart";
        c1.number = 7;

        Card c2 = new Card();
        c2.kind = "Spade";
        c2.number = 4;

        System.out.println("c1은 " + c1.kind + ", " + c1.number + "이며, 크기는 (" + c1.width + ", " + c1.height + ")" );
        System.out.println("c2는 " + c2.kind + ", " + c2.number + "이며, 크기는 (" + c2.width + ", " + c2.height + ")" );

        System.out.println("c1의 width와 height를 각각 50, 80으로 변경합니다.");
        c1.width = 50;
        c1.height = 80;

        System.out.println("c1은 " + c1.kind + ", " + c1.number + "이며, 크기는 (" + c1.width + ", " + c1.height + ")" );
        System.out.println("c2는 " + c2.kind + ", " + c2.number + "이며, 크기는 (" + c2.width + ", " + c2.height + ")" );
    }
}

class Card {
    String kind ;
    int number;
    static int width = 100;
    static int height = 250;
}
```
```
Card.width = 100
Card.height = 250
c1은 Heart, 7이며, 크기는 (100, 250)
c2는 Spade, 4이며, 크기는 (100, 250)
c1의 width와 height를 각각 50, 80으로 변경합니다.
c1은 Heart, 7이며, 크기는 (50, 80)
c2는 Spade, 4이며, 크기는 (50, 80)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
클래스에서 메서드는 다음과 같이 호출할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-6
```java
class MyMathTest {
    public static void main(String args[]) {
        MyMath mm = new MyMath();
        long result1 = mm.add(5L, 3L);
        long result2 = mm.subtract(5L, 3L);
        long result3 = mm.multiply(5L, 3L);
        double result4 = mm.divide(5L, 3L);
        System.out.println("add(5L, 3L) = "      + result1);
        System.out.println("subtract(5L, 3L) = " + result2);
        System.out.println("multiply(5L, 3L) = " + result3);
        System.out.println("divide(5L, 3L) = "   + result4);
    }
}

class MyMath {
    long add(long a, long b) {
        long result = a+b;
        return result;
        //	return a + b;	// 위의 두 줄을 이와 같이 한 줄로 간단히 할 수 있다.
    }

    long subtract(long a, long b) {
        return a - b;
    }

    long multiply(long a, long b) {
        return a * b;
    }

    double divide(double a, double b) {
        return a / b;
    }
}
```
```
add(5L, 3L) = 8
subtract(5L, 3L) = 2
multiply(5L, 3L) = 15
divide(5L, 3L) = 1.6666666666666667

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
JVM의 메모리는 메서드 영역, 힙, 호출스택으로 구성되어 있습니다. 그 중 호출스택에 대한 두개의 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-7
```java
class CallStackTest {
    public static void main(String[] args) {
        firstMethod();
    }

    static void firstMethod() {
        secondMethod();
    }

    static void secondMethod() {
        System.out.println("secondMethod()");
    }
}
```
```
secondMethod()

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-8
```java
class CallStackTest2 {
    public static void main(String[] args) {
        System.out.println("main(String[] args)이 시작되었음.");
        firstMethod();
        System.out.println("main(String[] args)이 끝났음.");
    }

    static void firstMethod() {
        System.out.println("firstMethod()이 시작되었음.");
        secondMethod();
        System.out.println("firstMethod()이 끝났음.");
    }

    static void secondMethod() {
        System.out.println("secondMethod()이 시작되었음.");
        System.out.println("secondMethod()이 끝났음.");
    }
}
```
```
main(String[] args)이 시작되었음.
firstMethod()이 시작되었음.
secondMethod()이 시작되었음.
secondMethod()이 끝났음.
firstMethod()이 끝났음.
main(String[] args)이 끝났음.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
기본형 매개변수는 다음과 같이 사용할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-9
```java
class Data { int x; }

class PrimitiveParamEx {
    public static void main(String[] args) {
        Data d = new Data();
        d.x = 10;
        System.out.println("main() : x = " + d.x);

        change(d.x);
        System.out.println("After change(d.x)");
        System.out.println("main() : x = " + d.x);
    }

    static void change(int x) {  // 기본형 매개변수
        x = 1000;
        System.out.println("change() : x = " + x);
    }
}
```
```
main() : x = 10
change() : x = 1000
After change(d.x)
main() : x = 10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
참조형 매개변수에 대한 예시는 다음과 같이 3가지가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-10
```java
class Data { int x; }

class ReferenceParamEx {
    public static void main(String[] args) {

        Data d = new Data();
        d.x = 10;
        System.out.println("main() : x = " + d.x);

        change(d);
        System.out.println("After change(d)");
        System.out.println("main() : x = " + d.x);

    }

    static void change(Data d) { // 참조형 매개변수
        d.x = 1000;
        System.out.println("change() : x = " + d.x);
    }
}
```
```
main() : x = 10
change() : x = 1000
After change(d)
main() : x = 1000

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-11
```java
class ReferenceParamEx2 {
    public static void main(String[] args)
    {
        int[] x = {10};  // 크기가 1인 배열. x[0] = 10;
        System.out.println("main() : x = " + x[0]);

        change(x);
        System.out.println("After change(x)");
        System.out.println("main() : x = " + x[0]);
    }

    static void change(int[] x) { // 참조형 매개변수
        x[0] = 1000;
        System.out.println("change() : x = " + x[0]);
    }
}
```
```
main() : x = 10
change() : x = 1000
After change(x)
main() : x = 1000

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-12
```java
class ReferenceParamEx3 {
    public static void main(String[] args)
    {
        int[] arr = new int[] {3,2,1,6,5,4};

        printArr(arr);  // 배열의 모든 요소를 출력
        sortArr(arr);   // 배열을 정렬
        printArr(arr);  // 정렬후 결과를 출력
        System.out.println("sum="+sumArr(arr)); // 배열의 총합을 출력
    }

    static void printArr(int[] arr) {  // 배열의 모든 요소를 출력
        System.out.print("[");

        for(int i : arr)  // 향상된 for문
            System.out.print(i+",");
        System.out.println("]");
    }

    static int sumArr(int[] arr) {  // 배열의 모든 요소의 합을 반환
        int sum = 0;

        for(int i=0;i<arr.length;i++)
            sum += arr[i];
        return sum;
    }

    static void sortArr(int[] arr) {  // 배열의 오름차순으로 정렬
        for(int i=0;i<arr.length-1;i++)
            for(int j=0;j<arr.length-1-i;j++)
                if(arr[j] > arr[j+1]) {
                    int tmp = arr[j];
                    arr[j] = arr[j+1];
                    arr[j+1] = tmp;
                }
    } // sortArr(int[] arr)
}
```
```
[3,2,1,6,5,4,]
[1,2,3,4,5,6,]
sum=21

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음 예제는 반환값이 있는 메서드를 반환값이 없는 메서드로 바꾸는 방법을 보여줍니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-13
```java
class ReturnTest {
    public static void main(String[] args) {
        ReturnTest r = new ReturnTest();

        int result = r.add(3,5);
        System.out.println(result);

        int[] result2 = {0}; // 배열을 생성하고 result2[0]의 값을 0으로 초기화
        r.add(3,5,result2);  // 배열을 add메서드의 매개변수로 전달
        System.out.println(result2[0]);
    }

    int add(int a, int b) {
        return a + b;
    }

    void add(int a, int b, int[] result) {
        result[0] = a + b;  // 매개변수로 넘겨받은 배열에 연산결과를 저장
    }
}
```
```
8
8

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
참조형 반환타입에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-14
```java
class Data { int x; }

class ReferenceReturnEx {
    public static void main(String[] args)
    {
        Data d = new Data();
        d.x = 10;

        Data d2 = copy(d);
        System.out.println("d.x ="+d.x);
        System.out.println("d2.x="+d2.x);
    }

    static Data copy(Data d) {
        Data tmp = new Data();
        tmp.x = d.x;

        return tmp;
    }
}
```
```
d.x =10
d2.x=10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
재귀호출을 하는 예시는 다음 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-15
```java
class FactorialTest {
    public static void main(String args[]) {
        System.out.println(factorial(4)); // FactorialTest.factorial(4)
    }

    static long factorial(int n) {
        long result=0;

        if (n == 1) return 1;

        return n * factorial(n-1); // 다시 메서드 자신을 호출한다.
    }
}
```
```
24

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-16
```java
class FactorialTest2 {
    static long factorial(int n) {
        if(n<=0 || n>20) return -1;  // 매개변수의 유효성 검사.
        if(n<=1)
            return 1;
        return n * factorial(n-1);
    }

    public static void main(String args[]) {
        int  n = 21;
        long result = 0;

        for(int i = 1; i <= n; i++) {
            result = factorial(i);

            if(result==-1) {
                System.out.printf("유효하지 않은 값입니다.(0<n<=20):%d%n", n);
                break;
            }

            System.out.printf("%2d!=%20d%n", i, result);
        }
    } // main의 끝
}
```
```
 1!=                   1
 2!=                   2
 3!=                   6
 4!=                  24
 5!=                 120
 6!=                 720
 7!=                5040
 8!=               40320
 9!=              362880
10!=             3628800
11!=            39916800
12!=           479001600
13!=          6227020800
14!=         87178291200
15!=       1307674368000
16!=      20922789888000
17!=     355687428096000
18!=    6402373705728000
19!=  121645100408832000
20!= 2432902008176640000
유효하지 않은 값입니다.(0<n<=20):21

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음 예시는 무한 호출로 인한 에러를 보여줍니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-17
```java
class MainTest {
    public static void main(String args[]) {
        main(null);		// 재귀호출. 자기 자신을 다시 호출한다.
    }
}
```
```
Exception in thread "main" java.lang.StackOverflowError
	at MainTest.main(HelloWorldPrint.java:3)
	at MainTest.main(HelloWorldPrint.java:3)
	at MainTest.main(HelloWorldPrint.java:3)
  ...
  at MainTest.main(HelloWorldPrint.java:3)
	at MainTest.main(HelloWorldPrint.java:3)
	at MainTest.main(HelloWorldPrint.java:3)

Process finished with exit code 1
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-18
```java
class PowerTest {
    public static void main(String[] args) {
        int x = 2;
        int n = 5;
        long result = 0;

        for(int i=1; i<=n; i++) {
            result += power(x, i);
        }

        System.out.println(result);
    }

    static long power(int x, int n) {
        if(n==1) return x;

        return x * power(x, n-1);
    }
}
```
```
62

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 클래스 메서드와 인스턴스 메서드에 대해 알아보겠습니다. 변수와 동일한 방식으로, 메서드 앞에 static이 붙어 있으면 클래스 메서드이고, 붙어 있지 않다면 인스턴스 메서드임을 알 수 있습니다. 하단의 코드는 클래스 메서드와 인스턴스 메서드 활용 사례입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-19
```java
class MyMath2 {
    long a, b;

    // 인스턴스 변수 a, b만을 이용해서 작업하므로 매개변수가 필요없다.
    long add() 	    { return a + b; }  // a, b는 인스턴스변수
    long subtract() { return a - b; }
    long multiply() { return a * b; }
    double divide() { return a / b; }

    // 인스턴스변수와 관계없이 매개변수로만 작업이 가능하다.
    static long   add(long a, long b) 	   	 { return a + b; } // a, b는 지역변수
    static long   subtract(long a, long b)   { return a - b; }
    static long   multiply(long a, long b)	 { return a * b; }
    static double divide(double a, double b) { return a / b; }
}

class MyMathTest2 {
    public static void main(String args[]) {
        // 클래스메서드 호출. 인스턴스 생성없이 호출가능
        System.out.println(MyMath2.add(200L, 100L));
        System.out.println(MyMath2.subtract(200L, 100L));
        System.out.println(MyMath2.multiply(200L, 100L));
        System.out.println(MyMath2.divide(200.0, 100.0));

        MyMath2 mm = new MyMath2(); // 인스턴스를 생성
        mm.a = 200L;
        mm.b = 100L;
        // 인스턴스메서드는 객체생성 후에만 호출이 가능함.
        System.out.println(mm.add());
        System.out.println(mm.subtract());
        System.out.println(mm.multiply());
        System.out.println(mm.divide());
    }
}
```
```
300
100
20000
2.0
300
100
20000
2.0

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음은 클래스 멤버와 인스턴스 멤버 간의 참조와 호출 가능 여부에 대한 예시입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-20
```java
class MemberCall {
    int iv = 10;
    static int cv = 20;

    int iv2 = cv;
    //	static int cv2 = iv;		// 에러. 클래스변수는 인스턴스 변수를 사용할 수 없음.
    static int cv2 = new MemberCall().iv;	 // 이처럼 객체를 생성해야 사용가능.

    static void staticMethod1() {
        System.out.println(cv);
//		System.out.println(iv); // 에러. 클래스메서드에서 인스턴스변수를 사용불가.
        MemberCall c = new MemberCall();
        System.out.println(c.iv);   // 객체를 생성한 후에야 인스턴스변수의 참조가능.
    }

    void instanceMethod1() {
        System.out.println(cv);
        System.out.println(iv); // 인스턴스메서드에서는 인스턴스변수를 바로 사용가능.
    }

    static void staticMethod2() {
        staticMethod1();
//		instanceMethod1(); // 에러. 클래스메서드에서는 인스턴스메서드를 호출할 수 없음.
        MemberCall c = new MemberCall();
        c.instanceMethod1(); // 인스턴스를 생성한 후에야 호출할 수 있음.
    }

    void instanceMethod2() {	// 인스턴스메서드에서는 인스턴스메서드와 클래스메서드
        staticMethod1();		//  모두 인스턴스 생성없이 바로 호출이 가능하다.
        instanceMethod1();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 오버로딩에 대해 알아보겠습니다. 오버로딩이란 한 클래스 내에 같은 이름의 메서드를 여러 개 정의하는 것을 의미합니다. 메서드 오버로딩으로도 불리며, 하나의 메서드 이름으로 여러 기능을 구현할 수 있는 특징이 있습니다.
________________________________________________________________________________________________________________________________________________________________________
다음 예시가 오버로딩의 사례라고 볼 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-21
```java
class OverloadingTest {
    public static void main(String args[]) {
        MyMath3 mm = new MyMath3();
        System.out.println("mm.add(3, 3) 결과:"    + mm.add(3,3));
        System.out.println("mm.add(3L, 3) 결과: "  + mm.add(3L,3));
        System.out.println("mm.add(3, 3L) 결과: "  + mm.add(3,3L));
        System.out.println("mm.add(3L, 3L) 결과: " + mm.add(3L,3L));

        int[] a = {100, 200, 300};
        System.out.println("mm.add(a) 결과: " + mm.add(a));
    }
}

class MyMath3 {
    int add(int a, int b) {
        System.out.print("int add(int a, int b) - ");
        return a+b;
    }

    long add(int a, long b) {
        System.out.print("long add(int a, long b) - ");
        return a+b;
    }

    long add(long a, int b) {
        System.out.print("long add(long a, int b) - ");
        return a+b;
    }

    long add(long a, long b) {
        System.out.print("long add(long a, long b) - ");
        return a+b;
    }

    int add(int[] a) {		// 배열의 모든 요소의 합을 결과로 돌려준다.
        System.out.print("int add(int[] a) - ");
        int result = 0;
        for(int i=0; i < a.length;i++) {
            result += a[i];
        }
        return result;
    }
}
```
```
int add(int a, int b) - mm.add(3, 3) 결과:6
long add(long a, int b) - mm.add(3L, 3) 결과: 6
long add(int a, long b) - mm.add(3, 3L) 결과: 6
long add(long a, long b) - mm.add(3L, 3L) 결과: 6
int add(int[] a) - mm.add(a) 결과: 600

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
가변인자를 같이 활용한 사례는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-22
```java
class VarArgsEx {
    public static void main(String[] args) {
        String[] strArr = { "100", "200", "300" };

        System.out.println(concatenate("", "100", "200", "300"));
        System.out.println(concatenate("-", strArr));
        System.out.println(concatenate(",", new String[]{"1", "2", "3"}));
        System.out.println("["+concatenate(",", new String[0])+"]");
        System.out.println("["+concatenate(",")+"]");
    }

    static String concatenate(String delim, String... args) {
        String result = "";

        for(String str : args) {
            result += str + delim;
        }

        return result;
    }

/*
	static String concatenate(String... args) {
		return concatenate("",args);
	}
*/
} // class
```
```
100200300
100-200-300-
1,2,3,
[]
[]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 생성자에 대해 알아보겠습니다. 생성자란 인스턴스가 생성될 때 호출되는 '인스턴스 초기화 메서드'입니다. 따라서 인스턴스 변수의 초기화 작업에 주로 사용되며, 인스턴스 생성 시에 실행되어야 할 작업을 위해서도 사용됩니다.
________________________________________________________________________________________________________________________________________________________________________
기본 생성자에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-23
```java
class Data1 {
    int value;
}

class Data2 {
    int value;

    Data2(int x) {
        value = x;
    }
}

class ConstructorTest {
    public static void main(String[] args) {
        Data1 d1 = new Data1();
        Data2 d2 = new Data2();		// compile error발생
    }
}
```
```
java: constructor Data2 in class Data2 cannot be applied to given types;
  required: int
  found: no arguments
  reason: actual and formal argument lists differ in length
```
________________________________________________________________________________________________________________________________________________________________________
위와 같이, Data2에서 에러가 발생합니다. 그 이유는 Data1에는 정의되어 있는 생성자가 하나도 없으므로 컴파일러가 기본 생성자를 추가해주었지만, Data2에는 이미 생성자 Data(int x)가 정의되어 있기 때문입니다.
________________________________________________________________________________________________________________________________________________________________________
매개변수가 있는 생성자를 활용한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-24
```java
class Car {
    String color;		// 색상
    String gearType;	// 변속기 종류 - auto(자동), manual(수동)
    int door;			// 문의 개수

    Car() {}
    Car(String c, String g, int d) {
        color = c;
        gearType = g;
        door = d;
    }
}

class CarTest {
    public static void main(String[] args) {
        Car c1 = new Car();
        c1.color = "white";
        c1.gearType = "auto";
        c1.door = 4;

        Car c2 = new Car("white", "auto", 4);

        System.out.println("c1의 color=" + c1.color + ", gearType=" + c1.gearType+ ", door="+c1.door);
        System.out.println("c2의 color=" + c2.color + ", gearType=" + c2.gearType+ ", door="+c2.door);
    }
}
```
```
c1의 color=white, gearType=auto, door=4
c2의 color=white, gearType=auto, door=4

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
생성자에서 다른 생성자를 호출할 때는 this() 혹은 this를 사용할 수 있습니다. 다음 예시를 통해 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-25
```java
class Car {
    String color;		// 색상
    String gearType;	// 변속기 종류 - auto(자동), manual(수동)
    int door;			// 문의 개수

    Car() {
        this("white", "auto", 4);
    }

    Car(String color) {
        this(color, "auto", 4);
    }
    Car(String color, String gearType, int door) {
        this.color    = color;
        this.gearType = gearType;
        this.door     = door;
    }
}

class CarTest2 {
    public static void main(String[] args) {
        Car c1 = new Car();
        Car c2 = new Car("blue");

        System.out.println("c1의 color=" + c1.color + ", gearType=" + c1.gearType+ ", door="+c1.door);
        System.out.println("c2의 color=" + c2.color + ", gearType=" + c2.gearType+ ", door="+c2.door);
    }
}
```
```
c1의 color=white, gearType=auto, door=4
c2의 color=blue, gearType=auto, door=4

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
생성자를 이용한 인스턴스의 복사를 진행한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-26
```java
class Car {
    String color;		// 색상
    String gearType;    // 변속기 종류 - auto(자동), manual(수동)
    int door;			// 문의 개수

    Car() {
        this("white", "auto", 4);
    }

    Car(Car c) {	// 인스턴스의 복사를 위한 생성자.
        color    = c.color;
        gearType = c.gearType;
        door     = c.door;
    }

    Car(String color, String gearType, int door) {
        this.color    = color;
        this.gearType = gearType;
        this.door     = door;
    }
}
class CarTest3 {
    public static void main(String[] args) {
        Car c1 = new Car();
        Car c2 = new Car(c1);	// c1의 복사본 c2를 생성한다.
        System.out.println("c1의 color=" + c1.color + ", gearType=" + c1.gearType+ ", door="+c1.door);
        System.out.println("c2의 color=" + c2.color + ", gearType=" + c2.gearType+ ", door="+c2.door);

        c1.door=100;	// c1의 인스턴스변수 door의 값을 변경한다.
        System.out.println("c1.door=100; 수행 후");
        System.out.println("c1의 color=" + c1.color + ", gearType=" + c1.gearType+ ", door="+c1.door);
        System.out.println("c2의 color=" + c2.color + ", gearType=" + c2.gearType+ ", door="+c2.door);
    }
}
```
```
c1의 color=white, gearType=auto, door=4
c2의 color=white, gearType=auto, door=4
c1.door=100; 수행 후
c1의 color=white, gearType=auto, door=100
c2의 color=white, gearType=auto, door=4

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
이제 변수의 초기화 사례에 대해 확인해보겠습니다. 변수의 초기화란 변수를 선언하고 처음으로 값을 저장하는 것을 의미합니다. 다음 4개의 예제가 이에 해당되는 예시입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-27
```java
class BlockTest {

    static {
        System.out.println("static { }");
    }

    {
        System.out.println("{ }");
    }

    public BlockTest() {
        System.out.println("생성자");
    }

    public static void main(String args[]) {
        System.out.println("BlockTest bt = new BlockTest(); ");
        BlockTest bt = new BlockTest();

        System.out.println("BlockTest bt2 = new BlockTest(); ");
        BlockTest bt2 = new BlockTest();
    }
}
```
```
static { }
BlockTest bt = new BlockTest(); 
{ }
생성자
BlockTest bt2 = new BlockTest(); 
{ }
생성자

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-28
```java
class StaticBlockTest
{
    static int[] arr = new int[10];

    static {
        for(int i=0;i<arr.length;i++) {
            // 1과 10사이의 임의의 값을 배열 arr에 저장한다.
            arr[i] = (int)(Math.random()*10) + 1;
        }
    }

    public static void main(String args[]) {
        for(int i=0; i<arr.length;i++)
            System.out.println("arr["+i+"] :" + arr[i]);
    }
}
```
```
arr[0] :1
arr[1] :8
arr[2] :9
arr[3] :1
arr[4] :6
arr[5] :1
arr[6] :8
arr[7] :2
arr[8] :4
arr[9] :6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-29
```java
class Product {
    static int count = 0;   // 생성된 인스턴스의 수를 저장하기 위한 변수
    int serialNo;	        // 인스턴스 고유의 번호

    {
        ++count;
        serialNo = count;
    }

    public Product() {}     // 기본 생성자, 생략 가능
}

class ProductTest {
    public static void main(String args[]) {
        Product p1 = new Product();
        Product p2 = new Product();
        Product p3 = new Product();

        System.out.println("p1의 제품번호(serial no)는 " + p1.serialNo);
        System.out.println("p2의 제품번호(serial no)는 " + p2.serialNo);
        System.out.println("p3의 제품번호(serial no)는 " + p3.serialNo);
        System.out.println("생산된 제품의 수는 모두 "+Product.count+"개입니다.");
    }
}
```
```
p1의 제품번호(serial no)는 1
p2의 제품번호(serial no)는 2
p3의 제품번호(serial no)는 3
생산된 제품의 수는 모두 3개입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 6-30
```java
class Document {
    static int count = 0;
    String name;     // 문서명(Document name)

    Document() {
        this("제목없음" + ++count);
    }

    Document(String name) {
        this.name = name;
        System.out.println("문서 " + this.name + "가 생성되었습니다.");
    }
}

class DocumentTest {
    public static void main(String args[]) {
        Document d1 = new Document();
        Document d2 = new Document("자바.txt");
        Document d3 = new Document();
        Document d4 = new Document();
    }
}
```
```
문서 제목없음1가 생성되었습니다.
문서 자바.txt가 생성되었습니다.
문서 제목없음2가 생성되었습니다.
문서 제목없음3가 생성되었습니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
우선 여기까지가 6장의 내용에 해당됩니다. 다음 포스팅에서도 객체지향 프로그래밍에 대해 계속 다루지만 7장의 범위를 다룰 예정입니다.
________________________________________________________________________________________________________________________________________________________________________
오늘도 긴 글 읽어주시느라 고생하셨습니다. 내일부터 새로운 한 주 알차게 보내시길 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=