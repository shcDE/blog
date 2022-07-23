---
title: "자바 공부 기록(4) - 배열에 대해 알아볼까요?"
date: 2022-07-23T14:12:38+09:00
categories:
- development
tags:
- development
keywords:
- backend, java, development
image: coffee-gab9b09d77_1920.jpg
---
![java](https://github.com/shcDE/pictures/blob/main/images_for_blog/coffee-gab9b09d77_1920.jpg?raw=true)
_________________________________________________________________________________________________________________________________________________________________________
# 자바에서 배열은 어떻게 사용할까요?
_________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 배열에 대해 다루겠습니다. 배열이란 같은 타입의 여러 변수를 하나의 묶음으로 다루는 것을 의미합니다. 많은 양의 데이터를 저장하기 위해서, 그 데이터의 숫자만큼 변수를 선언해야 한다면 매우 혼란스러울 것입니다. 10,000개의 데이터를 저장하기 위해 같은 수의 변수를 선언해야 한다면 상상하는 것만으로도 상당히 곤혹스러울 것입니다. 이런 경우에 배열을 사용하면 많은 양의 데이터를 손쉽게 다룰 수 있습니다. 그러면 이러한 배열을 어떻게 다루는지 지금부터 알아보도록 하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 5장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 대표적인 예제부터 확인해보겠습니다. 해당 예제는 길이가 5인 배열 score를 선언하고, 이에 따른 각 배열의 요소 내에 값을 삽입하여 출력하는 과정을 보여줍니다. 코드와 결과는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-1
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] score = new int[5];
        int k = 1;

        score[0] = 50;
        score[1] = 60;
        score[k+1] = 70;   // score[2] = 70
        score[3] = 80;
        score[4] = 90;

        int tmp = score[k+2] + score[4];  // int tmp = score[3] + score[4]

        // for문의 배열의 모든 요소를 출력한다.
        for(int i=0; i < 5; i++) {
            System.out.printf("score[%d]:%d%n",i, score[i]);
        }

        System.out.printf("tmp:%d%n", tmp);
        System.out.printf("score[%d]:%d%n",7,score[7]); //index의 범위를 벗어난 값
    }
}
```
```
score[0]:50
score[1]:60
score[2]:70
score[3]:80
score[4]:90
tmp:170
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 7
	at HelloWorldPrint.main(HelloWorldPrint.java:23)

Process finished with exit code 1
```
_________________________________________________________________________________________________________________________________________________________________________
'Arrays.toString' 메서드를 활용하면 배열의 출력을 더 간편하게 할 수 있습니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-2
```java
import java.util.*; // Arrays.toString()을 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] iArr1 = new int[10];
        int[] iArr2 = new int[10];
//		int[] iArr3 = new int[]{100, 95, 80, 70, 60};
        int[] iArr3 = {100, 95, 80, 70, 60};
        char[] chArr = {'a', 'b', 'c', 'd'};

        for (int i=0; i < iArr1.length ; i++ ) {
            iArr1[i] = i + 1; // 1~10의 숫자를 순서대로 배열에 넣는다.
        }

        for (int i=0; i < iArr2.length ; i++ ) {
            iArr2[i] = (int)(Math.random()*10) + 1; // 1~10의 값을 배열에 저장
        }

        // 배열에 저장된 값들을 출력한다.
        for(int i=0; i < iArr1.length;i++) {
            System.out.print(iArr1[i]+",");
        }
        System.out.println();
        System.out.println(Arrays.toString(iArr2));
        System.out.println(Arrays.toString(iArr3));
        System.out.println(Arrays.toString(chArr));
        System.out.println(iArr3);
        System.out.println(chArr);
    }
}
```
```
1,2,3,4,5,6,7,8,9,10,
[3, 8, 9, 7, 4, 10, 2, 1, 2, 8]
[100, 95, 80, 70, 60]
[a, b, c, d]
[I@7f31245a
abcd

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다음 두개의 예시는 배열을 복사하여 출력한 사례입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-3
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] arr = new int[5];

        // 배열 arr에 1~5를 저장한다.
        for(int i=0; i < arr.length;i++)
            arr[i] = i + 1;

        System.out.println("[변경전] - arr.length:"+arr.length);
        for(int i=0; i < arr.length;i++)
            System.out.println("arr["+i+"]:"+arr[i]);

        int[] tmp = new int[arr.length*2];

        // 배열 arr에 저장된 값들을 배열 tmp에 복사한다.
        for(int i=0; i < arr.length;i++)
            tmp[i] = arr[i];

        arr = tmp;  // tmp에 저장된 값을 arr에 저장한다.

        System.out.println("[변경후] - arr.length:"+arr.length);
        for(int i=0; i < arr.length;i++)
            System.out.println("arr["+i+"]:"+arr[i]);
    }
}
```
```
[변경전] - arr.length:5
arr[0]:1
arr[1]:2
arr[2]:3
arr[3]:4
arr[4]:5
[변경후] - arr.length:10
arr[0]:1
arr[1]:2
arr[2]:3
arr[3]:4
arr[4]:5
arr[5]:0
arr[6]:0
arr[7]:0
arr[8]:0
arr[9]:0

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-4
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        char[] abc = { 'A', 'B', 'C', 'D'};
        char[] num = { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};
        System.out.println(abc);
        System.out.println(num);

        // 배열 abc와 num을 붙여서 하나의 배열(result)로 만든다.
        char[] result = new char[abc.length+num.length];
        System.arraycopy(abc, 0, result, 0, abc.length);
        System.arraycopy(num, 0, result, abc.length, num.length);
        System.out.println(result);

        // 배열 abc를 배열 num의 첫번째 위치부터 배열 abc의 길이만큼 복사
        System.arraycopy(abc, 0, num, 0, abc.length);
        System.out.println(num);

        // number의 인덱스 6 위치에 3개를 복사
        System.arraycopy(abc, 0, num, 6, 3);
        System.out.println(num);
    }
}
```
```
ABCD
0123456789
ABCD0123456789
ABCD456789
ABCD45ABC9

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다음은 배열을 다양한 방법으로 활용한 사례입니다. 총 4개의 예제를 보여드리겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-5
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int sum =0;				// 총점을 저장하기 위한 변수
        float average = 0f;		// 평균을 저장하기 위한 변수

        int[] score = {100, 88, 100, 100, 90};

        for (int i=0; i < score.length ; i++ ) {
            sum += score[i];
        }

        average = sum / (float)score.length ; // 계산결과를 float으로 얻기 위해서 형변환

        System.out.println("총점 : " + sum);
        System.out.println("평균 : " + average);
    }
}
```
```
총점 : 478
평균 : 95.6

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-6
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] score = { 79, 88, 91, 33, 100, 55, 95};

        int max = score[0]; // 배열의 첫번째 값으로 최대값을 초기화한다.
        int min = score[0]; // 배열의 첫번째 값으로 최소값을 초기화한다.

        for(int i=1; i < score.length;i++) {
            if(score[i] > max) {
                max = score[i];
            } else if(score[i] < min) {
                min = score[i];
            }
        } // end of for

        System.out.println("최대값 :" + max);
        System.out.println("최소값 :" + min);
    }
}
```
```
최대값 :100
최소값 :33

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-7
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] numArr = new int[10];

        for (int i=0; i < numArr.length ; i++ ) {
            numArr[i] = i;  // 배열을 0~9의 숫자로 초기화한다.
            System.out.print(numArr[i]);
        }
        System.out.println();

        for (int i=0; i < 100; i++ ) {
            int n = (int)(Math.random() * 10);	// 0~9중의 한 값을 임의로 얻는다.

            int tmp = numArr[0];
            numArr[0] = numArr[n];
            numArr[n] = tmp;
        }

        for (int i=0; i < numArr.length ; i++ )
            System.out.print(numArr[i]);
    }
}
```
```
0123456789
1953782406
Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-8
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        // 45개의 정수값을 저장하기 위한 배열 생성.
        int[] ball = new int[45];

        // 배열의 각 요소에 1~45의 값을 저장한다.
        for(int i=0; i < ball.length; i++)
            ball[i] = i+1;    // ball[0]에 1이 저장된다.

        int temp = 0;  // 두 값을 바꾸는데 사용할 임시변수
        int j = 0;     // 임의의 값을 얻어서 저장할 변수

        // 배열의 i번째 요소와 임의의 요소에 저장된 값을 서로 바꿔서 값을 섞는다.
        // 0번째부터 5번째 요소까지 모두 6개만 바꾼다.
        for(int i=0; i < 6; i++) {
            j = (int)(Math.random() * 45); // 0~44범위의 임의의 값을 얻는다.
            temp     = ball[i];
            ball[i] = ball[j];
            ball[j] = temp;
        }

        // 배열 ball의 앞에서부터 6개의 요소를 출력한다.
        for(int i=0; i < 6; i++)
            System.out.printf("ball[%d]=%d%n", i, ball[i]);
    }
}
```
```
ball[0]=41
ball[1]=10
ball[2]=28
ball[3]=25
ball[4]=31
ball[5]=35

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
임의의 값으로 채우는 것은 random()만 사용하면 쉽게 할 수 있습니다. 다음 두개의 예시가 random()을 활용한 사례입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-9
```java
import java.util.*; // Arrays.toString()을 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] code = { -4, -1, 3, 6, 11 };  // 불연속적인 값들로 구성된 배열
        int[] arr = new int[10];

        for (int i=0; i < arr.length ; i++ ) {
            int tmp = (int)(Math.random() * code.length);
            arr[i] = code[tmp];
        }

        System.out.println(Arrays.toString(arr));
    }
}
```
```
[-1, 11, -1, -4, -1, 6, 6, -1, 11, 11]

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-10
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] numArr = new int[10];

        for (int i=0; i < numArr.length ; i++ ) {
            System.out.print(numArr[i] = (int)(Math.random() * 10));
        }
        System.out.println();

        for (int i=0; i < numArr.length-1 ; i++ ) {
            boolean changed = false;	// 자리바꿈이 발생했는지를 체크한다.

            for (int j=0; j < numArr.length-1-i ;j++) {
                if(numArr[j] > numArr[j+1]) { // 옆의 값이 작으면 서로 바꾼다.
                    int temp = numArr[j];
                    numArr[j] = numArr[j+1];
                    numArr[j+1] = temp;
                    changed = true;	// 자리바꿈이 발생했으니 changed를 true로.
                }
            } // end for j

            if (!changed) break;	// 자리바꿈이 없으면 반복문을 벗어난다.

            for(int k=0; k<numArr.length;k++)
                System.out.print(numArr[k]); // 정렬된 결과를 출력한다.
            System.out.println();
        } // end for i
    }
}
```
```
8811887343
8118873438
1188734388
1187343888
1173438888
1134378888
1133478888

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
버블정렬을 활용한 사례는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-11
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] numArr  = new int[10];
        int[] counter = new int[10];

        for (int i=0; i < numArr.length ; i++ ) {
            numArr[i] = (int)(Math.random() * 10); // 0~9의 임의의 수를 배열에 저장
            System.out.print(numArr[i]);
        }
        System.out.println();

        for (int i=0; i < numArr.length ; i++ ) {
            counter[numArr[i]]++;
        }

        for (int i=0; i < numArr.length ; i++ ) {
            System.out.println( i +"의 개수 :"+ counter[i]);
        }
    }
}
```
```
2995666272
0의 개수 :0
1의 개수 :0
2의 개수 :3
3의 개수 :0
4의 개수 :0
5의 개수 :1
6의 개수 :3
7의 개수 :1
8의 개수 :0
9의 개수 :2

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
배열 타입이 String인 경우에도 int 배열의 선언과 생성방법은 다르지 않습니다. 다음 두 예제는 String 배열에 대한 대표적인 예시를 보여주는 예제와 16진수를 2진수로 변환하는 예제입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-12
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        String[] names = {"Kim", "Park", "Yi"};

        for(int i=0; i < names.length;i++) {
            System.out.println("names["+i+"]:"+names[i]);
        }

        String tmp = names[2]; // 배열 names의 세번째 요소를 tmp에 저장
        System.out.println("tmp:"+tmp);

        names[0] = "Yu"; // 배열 names의 첫번째 요소를 "Yu"로 변경

        for(String str : names)   // 향상된 for문
            System.out.println(str);
    }
}
```
```
names[0]:Kim
names[1]:Park
names[2]:Yi
tmp:Yi
Yu
Park
Yi

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-13
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        char[] hex = { 'C', 'A', 'F', 'E'};

        String[] binary = {   "0000", "0001", "0010", "0011"
                , "0100", "0101", "0110", "0111"
                , "1000", "1001", "1010", "1011"
                , "1100", "1101", "1110", "1111" };

        String result="";

        for (int i=0; i < hex.length ; i++ ) {
            if(hex[i] >='0' && hex[i] <='9') {
                result +=binary[hex[i]-'0'];	   // '8'-'0'의 결과는 8이다.
            } else {	// A~F이면
                result +=binary[hex[i]-'A'+10]; // 'C'-'A'의 결과는 2
            }
        }

        System.out.println("hex:"+ new String(hex)); // String(char[] value)
        System.out.println("binary:"+result);
    }
}
```
```
hex:CAFE
binary:1100101011111110

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
char 배열과 String 클래스는 서로 변환이 가능합니다. 다음 두 예제를 통해 해당 사례를 확인할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-14
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        String src = "ABCDE";

        for(int i=0; i < src.length(); i++) {
            char ch = src.charAt(i); // src의 i번째 문자를 ch에 저장
            System.out.println("src.charAt("+i+"):"+ ch);
        }

        char[] chArr = src.toCharArray();  // String을 char[]로 변환

        System.out.println(chArr); // char배열(char[])을 출력
    }
}
```
```
src.charAt(0):A
src.charAt(1):B
src.charAt(2):C
src.charAt(3):D
src.charAt(4):E
ABCDE

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-15
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        String source = "SOSHELP";
        String[] morse = {".-", "-...", "-.-.","-..", "."
                ,"..-.", "--.", "....","..",".---"
                , "-.-", ".-..", "--", "-.", "---"
                , ".--.", "--.-",".-.","...","-"
                , "..-", "...-", ".--", "-..-","-.--"
                , "--.." };

        String result="";

        for (int i=0; i < source.length() ; i++ ) {
            result+=morse[source.charAt(i)-'A'];
        }
        System.out.println("source:"+ source);
        System.out.println("morse:"+result);
    }
}
```
```
source:SOSHELP
morse:...---.........-...--.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
예제 5-15를 통해 morse 부호로도 변환이 가능한 것을 보여주고 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
다음 두 예제는 커맨드라인을 통해 입력받은 input을 통해 결과를 출력하는 사례입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-16
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        System.out.println("매개변수의 개수:"+args.length);

        for(int i=0;i< args.length;i++) {
            System.out.println("args[" + i + "] = \""+ args[i] + "\"");
        }
    }
}
```
```
매개변수의 개수:3
args[0] = "abc"
args[1] = "123"
args[2] = "Hello World"

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-17
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        if (args.length !=3) {
            System.out.println("usage: java ArrayEx15 NUM1 OP NUM2");
            System.exit(0);
        }

        int num1 = Integer.parseInt(args[0]);   // 문자열을 숫자로 변환한다.
        char op = args[1].charAt(0);            // 문자열을 문자(char)로 변환한다.
        int num2 = Integer.parseInt(args[2]);

        int result = 0;

        switch(op) {    // switch문의 수식으로 char 타입의 변수도 가능하다.
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case 'x':
                result = num1 * num2;
                break;
            case '/':
                result = num1 / num2;
                break;
            default :
                System.out.println("지원되지 않는 연산입니다.");
        }

        System.out.println("결과:"+result);
    }
}
```
```
usage: java ArrayEx15 NUM1 OP NUM2

Process finished with exit code 0
```
```
결과:13

Process finished with exit code 0
```
```
결과:30

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
커맨드라인 사용법은 [여기](https://zombiecoder.tistory.com/23)를 참고하였습니다.
_________________________________________________________________________________________________________________________________________________________________________
2차원 배열을 사용하는 방식은 다음 두개의 예제와 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-18
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[][] score = {
                { 100, 100, 100}
                , { 20, 20, 20}
                , { 30, 30, 30}
                , { 40, 40, 40}
        };
        int sum = 0;

        for(int i=0;i < score.length;i++) {
            for(int j=0;j < score[i].length;j++) {
                System.out.printf("score[%d][%d]=%d%n", i, j, score[i][j]);
            }
        }

        for (int[] tmp : score) {
            for (int i : tmp) {
                sum += i;
            }
        }

        System.out.println("sum="+sum);
    }
}
```
```
score[0][0]=100
score[0][1]=100
score[0][2]=100
score[1][0]=20
score[1][1]=20
score[1][2]=20
score[2][0]=30
score[2][1]=30
score[2][2]=30
score[3][0]=40
score[3][1]=40
score[3][2]=40
sum=570

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-19
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[][] score = {
                { 100, 100, 100}
                , { 20, 20, 20}
                , { 30, 30, 30}
                , { 40, 40, 40}
                , { 50, 50, 50}
        };
        // 과목별 총점
        int korTotal = 0;
        int engTotal = 0;
        int mathTotal = 0;

        System.out.println("번호   국어   영어   수학   총점   평균 ");
        System.out.println("=============================");

        for(int i=0;i < score.length;i++) {
            int   sum = 0;       // 개인별 총점
            float avg = 0.0f;   // 개인별 평균

            korTotal  += score[i][0];
            engTotal  += score[i][1];
            mathTotal += score[i][2];
            System.out.printf("%3d", i+1);

            for(int j=0;j < score[i].length;j++) {
                sum += score[i][j];
                System.out.printf("%5d", score[i][j]);
            }

            avg = sum/(float)score[i].length;  // 평균계산
            System.out.printf("%5d %5.1f%n", sum, avg);
        }

        System.out.println("=============================");
        System.out.printf("총점:%3d %4d %4d%n", korTotal, engTotal, mathTotal);
    }
}
```
```
번호   국어   영어   수학   총점   평균 
=============================
  1  100  100  100  300 100.0
  2   20   20   20   60  20.0
  3   30   30   30   90  30.0
  4   40   40   40  120  40.0
  5   50   50   50  150  50.0
=============================
총점:240  240  240

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다차원 배열을 활용한 예제는 다음 4개와 같습니다. 해당 예제들을 토대로 기능을 추가하면서 실습하면 많은 도움이 될 것이라고 생각합니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-20
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String[] args) {
        final int SIZE = 10;
        int x = 0, y = 0;

        char[][] board = new char[SIZE][SIZE];
        byte[][] shipBoard = {
                //  1  2  3  4  5  6  7  8  9
                { 0, 0, 0, 0, 0, 0, 1, 0, 0 }, // 1
                { 1, 1, 1, 1, 0, 0, 1, 0, 0 }, // 2
                { 0, 0, 0, 0, 0, 0, 1, 0, 0 }, // 3
                { 0, 0, 0, 0, 0, 0, 1, 0, 0 }, // 4
                { 0, 0, 0, 0, 0, 0, 0, 0, 0 }, // 5
                { 1, 1, 0, 1, 0, 0, 0, 0, 0 }, // 6
                { 0, 0, 0, 1, 0, 0, 0, 0, 0 }, // 7
                { 0, 0, 0, 1, 0, 0, 0, 0, 0 }, // 8
                { 0, 0, 0, 0, 0, 1, 1, 1, 0 }, // 9
        };

        // 1행에 행번호를, 1열에 열번호를 저장한다.
        for(int i=1;i<SIZE;i++)
            board[0][i] = board[i][0] = (char)(i+'0');

        Scanner scanner = new Scanner(System.in);

        while(true) {
            System.out.printf("좌표를 입력하세요. (종료는 00)>");
            String input = scanner.nextLine(); // 화면입력받은 내용을 input에 저장

            if(input.length()==2) {   // 두 글자를 입력한 경우
                x = input.charAt(0) - '0';  // 문자를 숫자로 변환
                y = input.charAt(1) - '0';

                if(x==0 && y==0) // x와 y가 모두 0인 경우 종료
                    break;
            }

            if(input.length()!=2 || x <= 0 || x >= SIZE || y <= 0 || y >= SIZE){
                System.out.println("잘못된 입력입니다. 다시 입력해주세요.");
                continue;
            }

            // shipBoard[x-1][y-1]의 값이 1이면, 'O'을 board[x][y]에 저장한다.
            board[x][y] = shipBoard[x-1][y-1]==1 ? 'O' : 'X';

            // 배열 board의 내용을 화면에 출력한다.
            for(int i=0;i<SIZE;i++) {
                System.out.println(board[i]); // board[i]는 1차원 배열
            }
            System.out.println();
        }
    }
}
```
```
좌표를 입력하세요. (종료는 00)>1010
잘못된 입력입니다. 다시 입력해주세요.
좌표를 입력하세요. (종료는 00)>33
 123456789
1         
2         
3  X      
4         
5         
6         
7         
8         
9         

좌표를 입력하세요. (종료는 00)>00

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-21
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String[] args) {
        final int SIZE = 5;
        int x = 0 , y = 0;
        int num = 0;

        int[][] bingo = new int[SIZE][SIZE];
        Scanner scanner = new Scanner(System.in);

        // 배열의 모든 요소를 1부터 SIZE*SIZE까지의 숫자로 초기화
        for(int i=0;i<SIZE;i++) {
            for(int j=0;j<SIZE;j++) {
                bingo[i][j] = i*SIZE + j + 1;
            }
        }

        // 배열에 저장된 값을 뒤섞는다.(shuffle)
        for(int i=0;i<SIZE;i++) {
            for(int j=0;j<SIZE;j++) {
                x = (int)(Math.random() * SIZE);
                y = (int)(Math.random() * SIZE);

                // bingo[i][j]와 임의로 선택된 값(bingo[x][y])을 바꾼다.
                int tmp =  bingo[i][j];
                bingo[i][j] = bingo[x][y];
                bingo[x][y] = tmp;
            }
        }

        do {
            for(int i=0;i<SIZE;i++) {
                for(int j=0;j<SIZE;j++)
                    System.out.printf("%2d ", bingo[i][j]);
                System.out.println();
            }
            System.out.println();

            System.out.printf("1~%d의 숫자를 입력하세요. (종료:0)>", SIZE*SIZE);
            String tmp = scanner.nextLine(); // 화면에서 입력받은 내용을 tmp에 저장
            num = Integer.parseInt(tmp);     // 입력받은 문자열(tmp)를 숫자로 변환

            // 입력받은 숫자와 같은 숫자가 저장된 요소를 찾아서 0을 저장
            outer:
            for(int i=0;i<SIZE;i++) {
                for(int j=0;j<SIZE;j++) {
                    if(bingo[i][j]==num) {
                        bingo[i][j] = 0;
                        break outer; // 2중 반복문을 벗어난다.
                    }
                }
            }
        } while(num!=0);
    }
}
```
```
 1 21 19  6 13 
14 22 23  7 15 
16  4 11 10  3 
17  2  8 12  5 
25  9 20 24 18 

1~25의 숫자를 입력하세요. (종료:0)>1
 0 21 19  6 13 
14 22 23  7 15 
16  4 11 10  3 
17  2  8 12  5 
25  9 20 24 18 

1~25의 숫자를 입력하세요. (종료:0)>9
 0 21 19  6 13 
14 22 23  7 15 
16  4 11 10  3 
17  2  8 12  5 
25  0 20 24 18 

1~25의 숫자를 입력하세요. (종료:0)>0

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-22
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[][] m1 = {
                {1, 2, 3},
                {4, 5, 6}
        };

        int[][] m2 = {
                {1, 2},
                {3, 4},
                {5, 6}
        };

        final int ROW    = m1.length;      // m1의 행 길이
        final int COL    = m2[0].length;  // m2의 열 길이
        final int M2_ROW = m2.length;	    // m2의 행 길이

        int[][] m3 = new int[ROW][COL];

        // 행렬곱 m1 x m2의 결과를 m3에 저장
        for(int i=0;i<ROW;i++)
            for(int j=0;j<COL;j++)
                for(int k=0;k<M2_ROW;k++)
                    m3[i][j] += m1[i][k] * m2[k][j];

        // 행렬 m3을 출력
        for(int i=0;i<ROW;i++) {
            for(int j=0;j<COL;j++) {
                System.out.printf("%3d ", m3[i][j]);
            }
            System.out.println();
        }
    }
}
```
```
 22  28 
 49  64 

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 5-23
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String[] args) {
        String[][] words = {
                {"chair","의자"},			// words[0][0], words[0][1]
                {"computer","컴퓨터"},	// words[1][0], words[1][1]
                {"integer","정수"}		// words[2][0], words[2][1]
        };

        Scanner scanner = new Scanner(System.in);

        for(int i=0;i<words.length;i++) {
            System.out.printf("Q%d. %s의 뜻은?", i+1, words[i][0]);

            String tmp = scanner.nextLine();

            if(tmp.equals(words[i][1])) {
                System.out.printf("정답입니다.%n%n");
            } else {
                System.out.printf("틀렸습니다. 정답은 %s입니다.%n%n",words[i][1]);
            }
        } // for
    }
}
```
```
Q1. chair의 뜻은?dmdjljl
틀렸습니다. 정답은 의자입니다.

Q2. computer의 뜻은?컴퓨터
정답입니다.

Q3. integer의 뜻은?정수
정답입니다.


Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
이상으로 배열에 대한 모든 예시를 확인했습니다. 다음 게시물부터는 객체지향 프로그래밍에 대해 2-3개의 포스팅으로 나누어 다룰 예정입니다. 오늘도 긴 글 읽어주시느라 고생하셨습니다.
_________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
_________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=
2. java) intelliJ 에서 Command line 을 이용한 데이터 전달하기 : https://zombiecoder.tistory.com/23