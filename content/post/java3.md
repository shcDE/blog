---
title: "자바 공부 기록(3) - 조건문과 반복문에 대해 알아볼까요?"
date: 2022-07-22T14:06:10+09:00
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
# 조건문과 반복문은 어떻게 활용할까요?
_________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 자바에서 어떻게 조건문과 반복문을 활용하는지 알아보겠습니다. 여기에서 조건문이란 조건에 따라 다른 문장이 수행되도록 하고, 반복문은 특정 문장들을 반복해서 수행하는 역할을 합니다. 여기에서 우선 조건문부터 확인한 이후에 반복문을 확인할 예정이며, 이번 포스팅은 내용이 다소 긴 점 양해 부탁드립니다. 
_________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 4장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
_________________________________________________________________________________________________________________________________________________________________________
앞에서 말씀드린 바와 같이, 우선 조건문에 대해 알아보겠습니다. 조건문은 조건식과 문장을 포함하는 블럭 {}으로 구성되어 있으며, 조건식의 연산 결과에 따라 실행할 문장이 달라져서 프로그램의 실행 흐름을 변경할 수 있습니다. 이러한 조건문은 if문과 switch문, 두가지가 있으며, 주로 if문이 많이 사용됩니다. switch문은 처리할 경우의 수가 많을 때 if문에 비해 더 효율적이지만, 그 대신 제약이 많다는 점 참고해주시길 바랍니다.
_________________________________________________________________________________________________________________________________________________________________________
우선 if문을 먼저 보겠습니다. if문은 가장 기본적인 조건문이며, 다음과 같이 '조건식'과 '괄호 {}'로 이루어져 있습니다. 'if'의 뜻이 '만일 ~이라면...'이므로 '만일(if) 조건식이 참(true)이면 괄호 {} 안의 문장들을 수행하라.'라는 의미로 이해하면 됩니다.
_________________________________________________________________________________________________________________________________________________________________________
이러한 if문의 사용 예시는 다음과 같습니다. 자바에서 조건식의 결과는 반드시 true 또는 false이어야 한다는 사실을 확인해주시면 감사하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-1
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int x= 0;

        System.out.printf("x=%d 일 때, 참인 것은%n", x);

        if(x==0) System.out.println("x==0");
        if(x!=0) System.out.println("x!=0");
        if(!(x==0)) System.out.println("!(x==0)");
        if(!(x!=0)) System.out.println("!(x!=0)");
        System.out.println();

        x = 1;
        System.out.printf("x=%d 일 때, 참인 것은%n", x);

        if(x==0) System.out.println("x==0");
        if(x!=0) System.out.println("x!=0");
        if(!(x==0)) System.out.println("!(x==0)");
        if(!(x!=0)) System.out.println("!(x!=0)");
    }
}
```
```
x=0 일 때, 참인 것은
x==0
!(x!=0)

x=1 일 때, 참인 것은
x!=0
!(x==0)

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
여기에서 만약 괄호 {}가 코드에 없으면 어떻게 출력되는지 확인하겠습니다. 해당 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-2
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int input;

        System.out.print("숫자를 하나 입력하세요.>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine(); // 화면을 통해 입력받은 내용을 tmp에 저장
        input = Integer.parseInt(tmp);   // 입력받은 문자열(tmp)을 숫자로 변환

        if(input==0) {
            System.out.println("입력하신 숫자는 0입니다.");
        }

        if(input!=0)
            System.out.println("입력하신 숫자는 0이 아닙니다.");
        System.out.println("입력하신 숫자는 "+ input +"입니다.");
    } // main의 끝
}
```
```
숫자를 하나 입력하세요.>3
입력하신 숫자는 0이 아닙니다.
입력하신 숫자는 3입니다.

Process finished with exit code 0
```
```
숫자를 하나 입력하세요.>0
입력하신 숫자는 0입니다.
입력하신 숫자는 0입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________두번째 출력문과 세번째 출력문은 괄호 {} 안에 들어있지 않으므로, if문에는 두번째 출력문만 해당되게 출력됩니다. 그래서 세번째 출력문은 어떠한 상황에서도 항상 출력되는 결과를 확인할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
이제 if-else문에 대해 확인하겠습니다. if-else문을 통해 if문으로 적용이 되지 않는 결과를 else를 통해 출력할 수 있습니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-3
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int input;

        System.out.print("숫자를 하나 입력하세요.>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        input = Integer.parseInt(tmp);

        if(input==0) {
            System.out.println("입력하신 숫자는 0입니다.");
        } else {
            System.out.println("입력하신 숫자는 0이 아닙니다.");
        }
    } // main의 끝
}
```
```
숫자를 하나 입력하세요.>5
입력하신 숫자는 0이 아닙니다.

Process finished with exit code 0
```
```
숫자를 하나 입력하세요.>0
입력하신 숫자는 0입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
if-else문을 응용해서 3가지 이상의 조건이 있을 경우 else if문을 추가할 수 있습니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-4
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int score  = 0;   // 점수를 저장하기 위한 변수
        char grade =' ';  // 학점을 저장하기 위한 변수. 공백으로 초기화한다.

        System.out.print("점수를 입력하세요.>");
        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        score = Integer.parseInt(tmp);

        if (score >= 90) {         // score가 90점 보다 같거나 크면 A학점
            grade = 'A';
        } else if (score >=80) {   // score가 80점 보다 같거나 크면 B학점
            grade = 'B';
        } else if (score >=70) {   // score가 70점 보다 같거나 크면 C학점
            grade = 'C';
        } else {                   // 나머지는 D학점
            grade = 'D';
        }

        System.out.println("당신의 학점은 "+ grade +"입니다.");
    }
}
```
```
점수를 입력하세요.>70
당신의 학점은 C입니다.

Process finished with exit code 0
```
```
점수를 입력하세요.>63
당신의 학점은 D입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
if문은 중첩으로도 사용할 수 있습니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-5
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int  score = 0;
        char grade = ' ';
        char opt   = '0';

        System.out.print("점수를 입력해주세요.>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        score = Integer.parseInt(tmp);

        System.out.printf("당신의 점수는 %d입니다.", score);

        if (score >= 90) {		      // score가 90점 보다 같거나 크면 A학점(grade)
            grade = 'A';
            if (score >= 98) { 	      // 90점 이상 중에서도 98점 이상은 A+
                opt = '+';
            } else if (score < 94) {  // 90점 이상 94점 미만은 A-
                opt = '-';
            }
        } else if (score >= 80){	  // score가 80점 보다 같거나 크면 B학점(grade)
            grade = 'B';
            if (score >= 88) {
                opt = '+';
            } else if (score < 84)	{
                opt = '-';
            }
        } else {				      // 나머지는 C학점(grade)
            grade = 'C';
        }

        System.out.printf("당신의 학점은 %c%c입니다.%n", grade, opt);
    }
}
```
```
점수를 입력해주세요.>100
당신의 점수는 100입니다.당신의 학점은 A+입니다.

Process finished with exit code 0
```
```
점수를 입력해주세요.>81
당신의 점수는 81입니다.당신의 학점은 B-입니다.

Process finished with exit code 0
```
```
점수를 입력해주세요.>85
당신의 점수는 85입니다.당신의 학점은 B0입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
이제 switch문을 보겠습니다. switch문으로 계절을 출력하는 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-6
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int month = 0;

        System.out.print("현재 월을 입력하세요.>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        month = Integer.parseInt(tmp);

        switch(month) {
            case 3:
            case 4:
            case 5:
                System.out.println("현재의 계절은 봄입니다.");
                break;
            case 6: case 7: case 8:
                System.out.println("현재의 계절은 여름입니다.");
                break;
            case 9: case 10: case 11:
                System.out.println("현재의 계절은 가을입니다.");
                break;
            default:
                //		case 12:	case 1: case 2:
                System.out.println("현재의 계절은 겨울입니다.");
        }
    }
}
```
```
현재 월을 입력하세요.>3
현재의 계절은 봄입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
이러한 switch문은 다음 예시와 같이 알아보기 편하게 작성할 수 있습니다. 다음 예시는 컴퓨터와 사용자가 가위바위보를 하는 간단한 게임입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-7
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int user, com;

        System.out.print("가위(1), 바위(2), 보(3) 중 하나를 입력하세요.>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        user = Integer.parseInt(tmp);

        com = (int)(Math.random() * 3) + 1;

        System.out.println("당신은 "+ user +"입니다.");
        System.out.println("컴은  "+ com +"입니다.");

        switch(user-com) {
            case 2: case -1:
                System.out.println("당신이 졌습니다.");
                break;
            case 1: case -2:
                System.out.println("당신이 이겼습니다.");
                break;
            case 0:
                System.out.println("비겼습니다.");
                //			break;		// 마지막 문장이므로 break를 사용할 필요가 없다.
        }
    }
}
```
```
가위(1), 바위(2), 보(3) 중 하나를 입력하세요.>1
당신은 1입니다.
컴은  2입니다.
당신이 졌습니다.

Process finished with exit code 0
```
```
가위(1), 바위(2), 보(3) 중 하나를 입력하세요.>3
당신은 3입니다.
컴은  1입니다.
당신이 졌습니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
switch문으로 다음 3가지의 예시처럼 다양한 시도를 해볼 수 있습니다. 다음 세가지 예시는 각각 성별 판별, 학점 판별, 학점 판별 코드 개선 사례입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-8
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        char gender;
        String regNo = "";

        System.out.print("당신의 주민번호를 입력하세요.(011231-1111222)>");

        Scanner scanner = new Scanner(System.in);
        regNo = scanner.nextLine();

        gender = regNo.charAt(7); // 입력받은 번호의 8번째 문자를 gender에 저장

        switch(gender) {
            case '1':
            case '3':
                System.out.println("당신은 남자입니다.");
                break;
            case '2':
            case '4':
                System.out.println("당신은 여자입니다.");
                break;
            default:
                System.out.println("유효하지 않은 주민등록번호입니다..");;
        }
    }
}
```
```
당신의 주민번호를 입력하세요.(011231-1111222)>110101-2111222
당신은 여자입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-9
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int score  = 0;
        char grade = ' ';

        System.out.print("당신의 점수를 입력하세요.(1~100)>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        score = Integer.parseInt(tmp);

        switch(score) {
            case 100: case 99: case 98: case 97: case 96:
            case 95:  case 94: case 93: case 92: case 91:
            case 90 :
                grade = 'A';
                break;
            case 89: case 88: case 87: case 86:
            case 85: case 84: case 83: case 82: case 81:
            case 80 :
                grade = 'B';
                break;
            case 79: case 78: case 77: case 76:
            case 75: case 74: case 73: case 72: case 71:
            case 70 :
                grade = 'C';
                break;
            default :
                grade = 'F';
        } // end of switch

        System.out.println("당신의 학점은 "+ grade +"입니다.");
    }
}
```
```
당신의 점수를 입력하세요.(1~100)>82
당신의 학점은 B입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-10
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int score  = 0;
        char grade = ' ';

        System.out.print("당신의 점수를 입력하세요.(1~100)>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        score = Integer.parseInt(tmp);

        switch(score/10) {
            case 10:
            case 9 :
                grade = 'A';
                break;
            case 8 :
                grade = 'B';
                break;
            case 7 :
                grade = 'C';
                break;
            default :
                grade = 'F';
        } // end of switch

        System.out.println("당신의 학점은 "+ grade +"입니다.");
    }
}
```
```
당신의 점수를 입력하세요.(1~100)>82
당신의 학점은 B입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
이러한 switch문은 if문처럼 중첩도 가능합니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-11
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        char gender;
        String regNo = "";
        System.out.print("당신의 주민번호를 입력하세요.(011231-1111222)>");

        Scanner scanner = new Scanner(System.in);
        regNo = scanner.nextLine();

        gender = regNo.charAt(7); // 입력받은 번호의 8번째 문자를 gender에 저장

        switch(gender) {
            case '1':
            case '3':
                switch(gender) {
                    case '1':
                        System.out.println("당신은 2000년 이전에 출생한 남자입니다.");
                        break;
                    case '3':
                        System.out.println("당신은 2000년 이후에 출생한 남자입니다.");
                }
                break;    // 이 break문을 빼먹지 않도록 주의
            case '2':
            case '4':
                switch(gender) {
                    case '2':
                        System.out.println("당신은 2000년 이전에 출생한 여자입니다.");
                        break;
                    case '4':
                        System.out.println("당신은 2000년 이후에 출생한 여자입니다.");
                        break;
                }
                break;
            default:
                System.out.println("유효하지 않은 주민등록번호입니다.");;
        }
    }
}
```
```
당신의 주민번호를 입력하세요.(011231-1111222)>010101-4111222
당신은 2000년 이후에 출생한 여자입니다.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
이제 반복문에 대해 알아보겠습니다. 반복문은 어떤 작업이 반복적으로 수행되도록 할 때 사용되며, 반복문의 종류로는 for문과 while문, 그리고 do-while문이 있습니다. 지금부터 for문부터 차근차근 확인하겠습니다.
_________________________________________________________________________________________________________________________________________________________________________
해당 예시는 for문을 활용한 대표적인 사례입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-12
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1;i<=5;i++)
            System.out.println(i); // i의 값을 출력한다.

        for(int i=1;i<=5;i++)
            System.out.print(i);  // print()를 쓰면 가로로 출력된다.

        System.out.println();
    }
}
```
```
1
2
3
4
5
12345

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다음은 1부터 10까지의 합을 출력하는 예제입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-13
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int sum = 0;	// 합계를 저장하기 위한 변수.

        for(int i=1; i <= 10; i++) {
            sum += i ;	//	sum = sum + i;
            System.out.printf("1부터 %2d 까지의 합: %2d%n", i, sum);
        }
    }
}
```
```
1부터  1 까지의 합:  1
1부터  2 까지의 합:  3
1부터  3 까지의 합:  6
1부터  4 까지의 합: 10
1부터  5 까지의 합: 15
1부터  6 까지의 합: 21
1부터  7 까지의 합: 28
1부터  8 까지의 합: 36
1부터  9 까지의 합: 45
1부터 10 까지의 합: 55

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다음은 i와 j에 1부터 10까지의 수를 서로 반대로 출력시키는 예제입니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-14
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1,j=10;i<=10;i++,j--)
            System.out.printf("%d \t %d%n", i, j);
    }
}
```
```
1 	 10
2 	 9
3 	 8
4 	 7
5 	 6
6 	 5
7 	 4
8 	 3
9 	 2
10 	 1

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
for문을 활용하여 다음과 같이 다양한 연산도 수행할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-15
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        System.out.println("i \t 2*i \t 2*i-1 \t i*i \t 11-i \t i%3 \t i/3");
        System.out.println("----------------------------------------------------");

        for(int i=1;i<=10;i++) {
            System.out.printf("%d \t %d \t %d \t %d \t %d \t %d \t %d%n", i, 2*i, 2*i-1, i*i, 11-i, i%3, i/3);
        }
    }
}
```
```
i 	 2*i 	 2*i-1 	 i*i 	 11-i 	 i%3 	 i/3
----------------------------------------------------
1 	 2 	   1 	   1	   10	   1	   0
2 	 4 	   3 	   4 	   9	   2	   0
3 	 6 	   5 	   9 	   8	   0	   1
4 	 8 	   7 	   16 	 7	   1	   1
5 	 10 	 9 	   25 	 6	   2	   1
6 	 12 	 11 	 36 	 5	   0	   2
7 	 14 	 13 	 49 	 4	   1	   2
8 	 16 	 15 	 64 	 3	   2	   2
9 	 18 	 17 	 81 	 2	   0	   3
10 	 20 	 19 	 100 	 1	   1	   3

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
for문도 다음과 같이 중첩으로 사용할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-16
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1;i<=5;i++) {
            for(int j=1;j<=10;j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```
```
**********
**********
**********
**********
**********

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
위의 별찍기 예제를 응용하여 다음과 같이 출력도 할 수 있습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-17
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int num = 0;

        System.out.print("*을 출력할 라인의 수를 입력하세요.>");

        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();  // 화면을 통해 입력받은 내용을 tmp에 저장
        num = Integer.parseInt(tmp);      // 입력받은 문자열(tmp)을 숫자로 변환

        for(int i=0;i<num;i++) {
            for(int j=0;j<=i;j++) {
                System.out.print("*");
            }
            System.out.println();
        }
    }
}
```
```
*을 출력할 라인의 수를 입력하세요.>10
*
**
***
****
*****
******
*******
********
*********
**********

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
반복문을 통해서 구구단도 출력 가능합니다. 사용 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-18
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=2;i<=9;i++) {
            for(int j=1;j<=9;j++) {
                System.out.printf("%d x %d = %d%n", i, j, i*j);
            }
        }
    }
}
```
```
2 x 1 = 2
2 x 2 = 4
2 x 3 = 6
2 x 4 = 8
2 x 5 = 10
2 x 6 = 12
2 x 7 = 14
2 x 8 = 16
2 x 9 = 18
3 x 1 = 3
3 x 2 = 6
3 x 3 = 9
3 x 4 = 12
3 x 5 = 15
3 x 6 = 18
3 x 7 = 21
3 x 8 = 24
3 x 9 = 27
4 x 1 = 4
4 x 2 = 8
4 x 3 = 12
4 x 4 = 16
4 x 5 = 20
4 x 6 = 24
4 x 7 = 28
4 x 8 = 32
4 x 9 = 36
5 x 1 = 5
5 x 2 = 10
5 x 3 = 15
5 x 4 = 20
5 x 5 = 25
5 x 6 = 30
5 x 7 = 35
5 x 8 = 40
5 x 9 = 45
6 x 1 = 6
6 x 2 = 12
6 x 3 = 18
6 x 4 = 24
6 x 5 = 30
6 x 6 = 36
6 x 7 = 42
6 x 8 = 48
6 x 9 = 54
7 x 1 = 7
7 x 2 = 14
7 x 3 = 21
7 x 4 = 28
7 x 5 = 35
7 x 6 = 42
7 x 7 = 49
7 x 8 = 56
7 x 9 = 63
8 x 1 = 8
8 x 2 = 16
8 x 3 = 24
8 x 4 = 32
8 x 5 = 40
8 x 6 = 48
8 x 7 = 56
8 x 8 = 64
8 x 9 = 72
9 x 1 = 9
9 x 2 = 18
9 x 3 = 27
9 x 4 = 36
9 x 5 = 45
9 x 6 = 54
9 x 7 = 63
9 x 8 = 72
9 x 9 = 81

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
3개의 반복문이 중첩되어 있는 경우 다음과 같은 순서로 반복문이 출력됩니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-19
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1;i<=3;i++)
            for(int j=1;j<=3;j++)
                for(int k=1;k<=3;k++)
                    System.out.println(""+i+j+k);
    }
}
```
```
111
112
113
121
122
123
131
132
133
211
212
213
221
222
223
231
232
233
311
312
313
321
322
323
331
332
333

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다음 세개의 예제와 같이 두 변수를 한 쌍으로 묶어 출력하거나, 출력 순서대로 공백을 추가하거나, 배열을 적용해서 응용도 가능합니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-20
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1;i<=5;i++) {
            for(int j=1;j<=5;j++) {
                System.out.printf("[%d,%d]", i, j);
            }
            System.out.println();
        }
    }
}
```
```
[1,1][1,2][1,3][1,4][1,5]
[2,1][2,2][2,3][2,4][2,5]
[3,1][3,2][3,3][3,4][3,5]
[4,1][4,2][4,3][4,4][4,5]
[5,1][5,2][5,3][5,4][5,5]

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-21
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1;i<=5;i++) {
            for(int j=1;j<=5;j++) {
                if(i==j) {
                    System.out.printf("[%d,%d]", i, j);
                } else {
                    System.out.printf("%5c", ' ');
                }
            }
            System.out.println();
        }
    }
}
```
```
[1,1]                    
     [2,2]               
          [3,3]          
               [4,4]     
                    [5,5]

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-22
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int[] arr = {10,20,30,40,50};
        int sum = 0;

        for(int i=0;i<arr.length;i++) {
            System.out.printf("%d ", arr[i]);
        }
        System.out.println();

        for(int tmp : arr) {
            System.out.printf("%d ", tmp);
            sum += tmp;
        }
        System.out.println();
        System.out.println("sum="+sum);
    }
}
```
```
10 20 30 40 50 
10 20 30 40 50 
sum=150

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
다음으로 while문에 대해 알아보겠습니다. while문은 true와 false의 조건을 맞추는 방식으로 반복을 진행합니다. 대표적인 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-23
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int i= 5;

        while(i--!=0) {
            System.out.println(i + " - I can do it.");
        }
    }
}
```
```
4 - I can do it.
3 - I can do it.
2 - I can do it.
1 - I can do it.
0 - I can do it.

Process finished with exit code 0
```
_________________________________________________________________________________________________________________________________________________________________________
해당 예시 외에 응용한 4개의 예시는 다음과 같습니다.
_________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-24
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int i=11;

        System.out.println("카운트다운을 시작합니다.");

        while(i--!=0) {
            System.out.println(i);

            for(int j=0;j<2_000_000_000;j++) {
                ;
            }
        }

        System.out.println("GAME OVER");
    }
}
```
```
카운트다운을 시작합니다.
10
9
8
7
6
5
4
3
2
1
0
GAME OVER
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-25
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int num = 0;
        int sum = 0;

        System.out.print("숫자를 입력하세요. (예:12345)>");
        Scanner scanner = new Scanner(System.in);
        String tmp = scanner.nextLine();
        num = Integer.parseInt(tmp);

        while(num!=0) {
            // num을 10으로 나눈 나머지를 sum에 더함
            sum += num%10; 	// sum = sum + num%10;
            System.out.printf("sum=%3d num=%d%n", sum, num);

            num /= 10;  // num = num / 10;  num을 10으로 나눈 값을 다시 num에 저장
        }

        System.out.println("각 자리수의 합:"+ sum);
    }
}
```
```
숫자를 입력하세요. (예:12345)>12345
sum=  5 num=12345
sum=  9 num=1234
sum= 12 num=123
sum= 14 num=12
sum= 15 num=1
각 자리수의 합:15

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-26
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int sum = 0;
        int i   = 0;

        while((sum += ++i) <= 100) { // i를 1씩 증가시켜서 sum에 계속 더해나간다.
            System.out.printf("%d - %d%n", i, sum);
        }
    }
}
```
```
1 - 1
2 - 3
3 - 6
4 - 10
5 - 15
6 - 21
7 - 28
8 - 36
9 - 45
10 - 55
11 - 66
12 - 78
13 - 91

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-27
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int num;
        int sum = 0;
        boolean flag = true;   // while문의 조건식으로 사용될 변수

        System.out.println("(합계를 구할 숫자를 입력하세요.(끝내려면 0을 입력)");

        while(flag) {    // flag의 값이 true이므로 조건식은 참이 된다.
            System.out.print(">>");

            Scanner scanner = new Scanner(System.in);
            String tmp = scanner.nextLine();
            num = Integer.parseInt(tmp);

            if(num!=0) {
                sum += num; // num이 0이 아니면, sum에 더한다.
            } else {
                flag = false;   // num이 0이면, flag의 값을 false로 변경.
            }
        } // while문의 끝

        System.out.println("합계:"+ sum);
    }
}
```
```
(합계를 구할 숫자를 입력하세요.(끝내려면 0을 입력)
>>100
>>200
>>300
>>400
>>0
합계:1000

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
while문의 변형인 do-while문은 while문과 반대로 블럭 {}을 먼저 수행한 후에 조건식을 평가합니다. 두개의 사용 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-28
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int input  = 0;
        int answer = 0;

        answer = (int)(Math.random() * 100) + 1; // 1~100사이의 임의의 수를 저장
        Scanner scanner = new Scanner(System.in);

        do {
            System.out.print("1과 100 사이의 정수를 입력하세요.>");

            String tmp = scanner.nextLine();
            input = Integer.parseInt(tmp);

            if(input > answer) {
                System.out.println("더 작은 수로 다시 시도해보세요");
            } else if(input < answer) {
                System.out.println("더 큰 수로 다시 시도해보세요.");
            }
        } while(input!=answer);

        System.out.println("정답입니다.");
    }
}
```
```
1과 100 사이의 정수를 입력하세요.>50
더 큰 수로 다시 시도해보세요.
1과 100 사이의 정수를 입력하세요.>70
더 큰 수로 다시 시도해보세요.
1과 100 사이의 정수를 입력하세요.>80
더 큰 수로 다시 시도해보세요.
1과 100 사이의 정수를 입력하세요.>90
더 큰 수로 다시 시도해보세요.
1과 100 사이의 정수를 입력하세요.>100
더 작은 수로 다시 시도해보세요
1과 100 사이의 정수를 입력하세요.>94
더 큰 수로 다시 시도해보세요.
1과 100 사이의 정수를 입력하세요.>95
정답입니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-29
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=1;i<=100;i++) {
            System.out.printf("i=%d ", i);

            int tmp = i;

            do {
                if(tmp%10%3==0 && tmp%10!=0) // tmp%10이 3의 배수인지 확인(0 제외)
                    System.out.print("짝");
            } while((tmp/=10)!=0);  // tmp /= 10은 tmp = tmp / 10과 동일

            System.out.println();
        }
    }
}
```
```
i=1 
i=2 
i=3 짝
i=4 
i=5 
i=6 짝
i=7 
i=8 
i=9 짝
i=10 
i=11 
i=12 
i=13 짝
i=14 
i=15 
i=16 짝
i=17 
i=18 
i=19 짝
i=20 
i=21 
i=22 
i=23 짝
i=24 
i=25 
i=26 짝
i=27 
i=28 
i=29 짝
i=30 짝
i=31 짝
i=32 짝
i=33 짝짝
i=34 짝
i=35 짝
i=36 짝짝
i=37 짝
i=38 짝
i=39 짝짝
i=40 
i=41 
i=42 
i=43 짝
i=44 
i=45 
i=46 짝
i=47 
i=48 
i=49 짝
i=50 
i=51 
i=52 
i=53 짝
i=54 
i=55 
i=56 짝
i=57 
i=58 
i=59 짝
i=60 짝
i=61 짝
i=62 짝
i=63 짝짝
i=64 짝
i=65 짝
i=66 짝짝
i=67 짝
i=68 짝
i=69 짝짝
i=70 
i=71 
i=72 
i=73 짝
i=74 
i=75 
i=76 짝
i=77 
i=78 
i=79 짝
i=80 
i=81 
i=82 
i=83 짝
i=84 
i=85 
i=86 짝
i=87 
i=88 
i=89 짝
i=90 짝
i=91 짝
i=92 짝
i=93 짝짝
i=94 짝
i=95 짝
i=96 짝짝
i=97 짝
i=98 짝
i=99 짝짝
i=100 

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
자바에서 break문을 사용하면 해당 break문이 있는 영역을 벗어나게 해줍니다. 사용 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-30
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        int sum = 0;
        int i   = 0;

        while(true) {
            if(sum > 100)
                break;
            ++i;
            sum += i;
        } // end of while

        System.out.println("i=" + i);
        System.out.println("sum=" + sum);
    }
}
```
```
i=14
sum=105

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
continue문은 반복문 내에서만 사용할 수 있으며, 반복이 진행되는 도중에 continue문을 만나면 반복문의 끝으로 이동하여 다음 반복으로 이동합니다. 사용 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-31
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        for(int i=0;i <= 10;i++) {
            if (i%3==0)
                continue;
            System.out.println(i);
        }
    }
}
```
```
1
2
4
5
7
8
10

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
continue문을 응용한 문제 해결 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-32
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int menu = 0;
        int num  = 0;

        Scanner scanner = new Scanner(System.in);

        while(true) {
            System.out.println("(1) square");
            System.out.println("(2) square root");
            System.out.println("(3) log");
            System.out.print("원하는 메뉴(1~3)를 선택하세요.(종료:0)>");

            String tmp = scanner.nextLine(); // 화면에서 입력받은 내용을 tmp에 저장
            menu = Integer.parseInt(tmp);    // 입력받은 문자열(tmp)을 숫자로 변환

            if(menu==0) {
                System.out.println("프로그램을 종료합니다.");
                break;
            } else if (!(1 <= menu && menu <= 3)) {
                System.out.println("메뉴를 잘못 선택하셨습니다. (종료는 0)");
                continue;
            }

            System.out.println("선택하신 메뉴는 "+ menu +"번입니다.");
        }
    }
}
```
```
(1) square
(2) square root
(3) log
원하는 메뉴(1~3)를 선택하세요.(종료:0)>4
메뉴를 잘못 선택하셨습니다. (종료는 0)
(1) square
(2) square root
(3) log
원하는 메뉴(1~3)를 선택하세요.(종료:0)>1
선택하신 메뉴는 1번입니다.
(1) square
(2) square root
(3) log
원하는 메뉴(1~3)를 선택하세요.(종료:0)>0
프로그램을 종료합니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
마지막으로, 반복문에는 이름을 붙일 수 있습니다. 해당 두개의 예시는 반복문에 이름을 붙여 결과를 출력한 사례입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-33
```java
public class HelloWorldPrint {
    public static void main(String[] args) {
        // for문에 Loop1이라는 이름을 붙였다.
        Loop1 : for(int i=2;i <=9;i++) {
            for(int j=1;j <=9;j++) {
                if(j==5)
                    break Loop1;
//						break;
//						continue Loop1;
//						continue;
                System.out.println(i+"*"+ j +"="+ i*j);
            } // end of for i
            System.out.println();
        } // end of Loop1
    }
}
```
```
2*1=2
2*2=4
2*3=6
2*4=8

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 4-34
```java
import java.util.*; // Scanner를 사용하기 위해 추가

public class HelloWorldPrint {
    public static void main(String[] args) {
        int menu = 0;
        int num  = 0;

        Scanner scanner = new Scanner(System.in);

        outer:
        while(true) {
            System.out.println("(1) square");
            System.out.println("(2) square root");
            System.out.println("(3) log");
            System.out.print("원하는 메뉴(1~3)를 선택하세요.(종료:0)>");

            String tmp = scanner.nextLine();
            menu = Integer.parseInt(tmp);

            if(menu==0) {
                System.out.println("프로그램을 종료합니다.");
                break;
            } else if (!(1<= menu && menu <= 3)) {
                System.out.println("메뉴를 잘못 선택하셨습니다. (종료는 0)");
                continue;
            }

            for(;;) {
                System.out.print("계산할 값을 입력하세요. (계산 종료:0, 전체 종료:99)>");
                tmp = scanner.nextLine();
                num = Integer.parseInt(tmp);

                if(num==0)
                    break;        // 계산 종료. for문을 벗어난다
                if(num==99)
                    break outer;  // 전체 종료. for문과 while문을 모두 벗어난다.

                switch(menu) {
                    case 1:
                        System.out.println("result="+ num*num);
                        break;
                    case 2:
                        System.out.println("result="+ Math.sqrt(num));
                        break;
                    case 3:
                        System.out.println("result="+ Math.log(num));
                        break;
                }
            } // for(;;)
        } // while의 끝
    } // main의 끝
}
```
```
(1) square
(2) square root
(3) log
원하는 메뉴(1~3)를 선택하세요.(종료:0)>1
계산할 값을 입력하세요. (계산 종료:0, 전체 종료:99)>2
result=4
계산할 값을 입력하세요. (계산 종료:0, 전체 종료:99)>3
result=9
계산할 값을 입력하세요. (계산 종료:0, 전체 종료:99)>0
(1) square
(2) square root
(3) log
원하는 메뉴(1~3)를 선택하세요.(종료:0)>2
계산할 값을 입력하세요. (계산 종료:0, 전체 종료:99)>4
result=2.0
계산할 값을 입력하세요. (계산 종료:0, 전체 종료:99)>99

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
이상으로 조건문과 반복문의 활용 사례에 대해 확인했습니다. 다음 포스팅에서는 '배열'에 대해 다룰 예정입니다. 모두 한 주 간 취업 준비 및 직장 생활 하시느라 고생하셨습니다. 즐거운 주말 되시길 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=