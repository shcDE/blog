---
title: "자바 공부 기록(7) - 예외처리에 대해 알아볼까요?"
date: 2022-07-26T16:29:18+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅은 예외처리에 대해 알아보겠습니다. 예외처리란 프로그램 실행 프로세스를 진행하는 동안 오류가 발생했을 때 그 오류를 대처하는 방법을 의미합니다. 여기에서 말하는 오류란 프로그램이 실행 중 어떤 원인에 의해서 오작동을 하거나 비정상적으로 종료되는 경우를 의미하며, 프로그램 에러라고도 불립니다. 지금부터 이러한 에러를 대처하는 방법인 예외처리를 어떻게 진행을 하면 되는지 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 8장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
________________________________________________________________________________________________________________________________________________________________________
예외처리는 다음과 같이 try-catch문을 활용해서 진행하는 케이스가 있습니다. 예제 8-1은 직접 작동되지 않지만, 예외처리를 try-catch문으로 어떻게 하는지 보여주는 대표적인 예시입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-1
```java
class ExceptionEx1 {
    public static void main(String[] args)
    {
        try  {
            try	{	} catch (Exception e)	{ }
        } catch (Exception e)	{
            try	{	} catch (Exception e) { }	// 에러. 변수 e가 중복 선언되었다.
        } // try-catch의 끝

        try  {

        } catch (Exception e)	{

        } // try-catch의 끝
    }	// main메서드의 끝
}
```
________________________________________________________________________________________________________________________________________________________________________
이러한 try-catch문을 활용하지 않으면 예제 8-2와 같이 에러가 발생합니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-2
```java
class ExceptionEx2 {
    public static void main(String args[]) {
        int number = 100;
        int result = 0;

        for(int i=0; i < 10; i++) {
            result = number / (int)(Math.random() * 10); // 7번째 라인
            System.out.println(result);
        }
    } // main의 끝
}
```
```
14
14
16
14
16
25
14
25
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at ExceptionEx2.main(HelloWorldPrint.java:7)

Process finished with exit code 1
```
________________________________________________________________________________________________________________________________________________________________________
예외처리를 통해 예제 8-2를 수정한 코드는 다음과 같습니다. 에러가 발생하는 부분에 "0"을 삽입하여 에러를 방지하였습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-3
```java
class ExceptionEx3 {
    public static void main(String args[]) {
        int number = 100;
        int result = 0;

        for(int i=0; i < 10; i++) {
            try {
                result = number / (int)(Math.random() * 10);
                System.out.println(result);
            } catch (ArithmeticException e)	{
                System.out.println("0");
            } // try-catch의 끝
        } // for의 끝
    }
}
```
```
12
0
16
33
12
25
0
50
11
12

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
try-catch문을 작성할 때 흐름의 따라 예외처리가 발생하지 않는 경우는 다음 사례와 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-4
```java
class ExceptionEx4 {
    public static void main(String args[]) {
        System.out.println(1);
        System.out.println(2);
        try {
            System.out.println(3);
            System.out.println(4);
        } catch (Exception e)	{
            System.out.println(5);
        } // try-catch의 끝
        System.out.println(6);
    }	// main메서드의 끝
}
```
```
1
2
3
4
6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예외처리를 다르게 적용시켜 4대신 5를 출력시키는 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-5
```java
class ExceptionEx5 {
    public static void main(String args[]) {
        System.out.println(1);
        System.out.println(2);
        try {
            System.out.println(3);
            System.out.println(0/0);
            System.out.println(4); 	// 실행되지 않는다.
        } catch (ArithmeticException ae)	{
            System.out.println(5);
        }	// try-catch의 끝
        System.out.println(6);
    }	// main메서드의 끝
}
```
```
1
2
3
5
6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예제 8-5를 변경하여 catch() 블럭을 활용한 사례는 다음과 같습니다. 결과는 동일하게 출력됩니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-6
```java
class ExceptionEx6 {
    public static void main(String args[]) {
        System.out.println(1);
        System.out.println(2);
        try {
            System.out.println(3);
            System.out.println(0/0);
            System.out.println(4); 	// 실행되지 않는다.
        } catch (Exception e)	{	// ArithmeticException대신 Exception을 사용
            System.out.println(5);
        }	// try-catch의 끝
        System.out.println(6);
    }	// main메서드의 끝
}
```
```
1
2
3
5
6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
catch 블럭을 활용한 또 다른 사례는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-7
```java
class ExceptionEx7 {
    public static void main(String args[]) {
        System.out.println(1);
        System.out.println(2);
        try {
            System.out.println(3);
            System.out.println(0/0);
            System.out.println(4); 		// 실행되지 않는다.
        } catch (ArithmeticException ae)	{
            if (ae instanceof ArithmeticException)
                System.out.println("true");
            System.out.println("ArithmeticException");
        } catch (Exception e)	{
            System.out.println("Exception");
        }	// try-catch의 끝
        System.out.println(6);
    }	// main메서드의 끝
}
```
```
1
2
3
true
ArithmeticException
6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
printStackTrace()와 getMessage()는 각각 예외발생 당시의 호출스택(Call Stack)에 있었던 메서드의 정보와 예외 메세지를 화면에 출력하는 역할과 발생한 예외클래스의 인스턴스에 저장된 메세지를 얻을 수 있게 도와주는 역할을 합니다. 사용 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-8
```java
class ExceptionEx8 {
    public static void main(String args[]) {
        System.out.println(1);
        System.out.println(2);
        try {
            System.out.println(3);
            System.out.println(0/0); // 예외발생!!!
            System.out.println(4); 	 // 실행되지 않는다.
        } catch (ArithmeticException ae)	{
            ae.printStackTrace();
            System.out.println("예외 메세지 : " + ae.getMessage());
        }	// try-catch의 끝
        System.out.println(6);
    }	// main메서드의 끝
}
```
```
1
2
3
예외 메세지 : / by zero
6
java.lang.ArithmeticException: / by zero
	at ExceptionEx8.main(HelloWorldPrint.java:7)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
throw를 사용하여 고의로 예외를 발생시킨 사례는 다음과 같이 3개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-9
```java
class ExceptionEx9 {
    public static void main(String args[]) {
        try {
            Exception e = new Exception("고의로 발생시켰음.");
            throw e;	 // 예외를 발생시킴
            //  throw new Exception("고의로 발생시켰음");

        } catch (Exception e)	{
            System.out.println("에러 메시지 : " + e.getMessage());
            e.printStackTrace();
        }
        System.out.println("프로그램이 정상 종료되었음");
    }
}
```
```
에러 메시지 : 고의로 발생시켰음.
프로그램이 정상 종료되었음
java.lang.Exception: 고의로 발생시켰음.
	at ExceptionEx9.main(HelloWorldPrint.java:4)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-10
```java
class ExceptionEx10 {
    public static void main(String[] args) {
        throw new Exception();		// Exception을 고의로 발생시킨다.
    }
}
```
```
java: unreported exception java.lang.Exception; must be caught or declared to be thrown
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-11
```java
class ExceptionEx11 {
    public static void main(String[] args) {
        throw new RuntimeException();	// RuntimeException을 고의로 발생시킨다.
    }
}
```
```
Exception in thread "main" java.lang.RuntimeException
	at ExceptionEx11.main(HelloWorldPrint.java:3)

Process finished with exit code 1
```
________________________________________________________________________________________________________________________________________________________________________
메서드에 예외를 선언하는 예시는 다음 5개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-12
```java
class ExceptionEx12 {
    public static void main(String[] args) throws Exception {
        method1();	 // 같은 클래스내의 static멤버이므로 객체생성없이 직접 호출가능.
    }	// main메서드의 끝

    static void method1() throws Exception {
        method2();
    }	// method1의 끝

    static void method2() throws Exception {
        throw new Exception();
    }	// method2의 끝
}
```
```
Exception in thread "main" java.lang.Exception
	at ExceptionEx12.method2(HelloWorldPrint.java:11)
	at ExceptionEx12.method1(HelloWorldPrint.java:7)
	at ExceptionEx12.main(HelloWorldPrint.java:3)

Process finished with exit code 1
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-13
```java
class ExceptionEx13 {
    public static void main(String[] args) {
        method1(); 	  // 같은 클래스 내의 static멤버이므로 객체생성없이 직접 호출가능
    }	// main메서드의 끝

    static void method1() {
        try {
            throw new Exception();
        } catch (Exception e) {
            System.out.println("method1메서드에서 예외가 처리되었습니다.");
            e.printStackTrace();
        }
    }	// method1의 끝
}
```
```
method1메서드에서 예외가 처리되었습니다.
java.lang.Exception
	at ExceptionEx13.method1(HelloWorldPrint.java:8)
	at ExceptionEx13.main(HelloWorldPrint.java:3)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-14
```java
class ExceptionEx14 {
    public static void main(String[] args) {
        try  {
            method1();
        } catch (Exception e)	{
            System.out.println("main메서드에서 예외가 처리되었습니다.");
            e.printStackTrace();
        }
    }	// main메서드의 끝

    static void method1() throws Exception {
        throw new Exception();
    }	// method1()의 끝
} // class의 끝
```
```
main메서드에서 예외가 처리되었습니다.
java.lang.Exception
	at ExceptionEx14.method1(HelloWorldPrint.java:12)
	at ExceptionEx14.main(HelloWorldPrint.java:4)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-15
```java
import java.io.*;

class ExceptionEx15 {
    public static void main(String[] args) {
        // command line에서 입력받은 값을 이름으로 갖는 파일을 생성한다.
        File f = createFile(args[0]);
        System.out.println( f.getName() + " 파일이 성공적으로 생성되었습니다.");
    } // main메서드의 끝

    static File createFile(String fileName) {
        try {
            if (fileName==null || fileName.equals(""))
                throw new Exception("파일이름이 유효하지 않습니다.");
        } catch (Exception e) {
            // fileName이 부적절한 경우, 파일 이름을 '제목없음.txt'로 한다.
            fileName = "제목없음.txt";
        } finally {
            File f = new File(fileName); // File클래스의 객체를 만든다.
            createNewFile(f);		     // 생성된 객체를 이용해서 파일을 생성한다.
            return f;
        }
    }	// createFile메서드의 끝

    static void createNewFile(File f) {
        try {
            f.createNewFile();		// 파일을 생성한다.
        } catch(Exception e){ }
    }	// createNewFile메서드의 끝
} 
```
```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: 0
	at ExceptionEx15.main(HelloWorldPrint.java:6)

Process finished with exit code 1
```
________________________________________________________________________________________________________________________________________________________________________
예제 8-15에서는 문서와 다르게 예외처리만 적용된다는 점 참고해주시길 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-16
```java
import java.io.*;

class ExceptionEx16 {
    public static void main(String[] args)
    {
        try {
            File f = createFile(args[0]);
            System.out.println( f.getName()+"파일이 성공적으로 생성되었습니다.");
        } catch (Exception e) {
            System.out.println(e.getMessage()+" 다시 입력해 주시기 바랍니다.");
        }
    }	// main메서드의 끝

    static File createFile(String fileName) throws Exception {
        if (fileName==null || fileName.equals(""))
            throw new Exception("파일이름이 유효하지 않습니다.");
        File f = new File(fileName);		//  File클래스의 객체를 만든다.
        // File객체의 createNewFile메서드를 이용해서 실제 파일을 생성한다.
        f.createNewFile();
        return f;		// 생성된 객체의 참조를 반환한다.
    }	// createFile메서드의 끝
}	// 클래스의 끝
```
```
0 다시 입력해 주시기 바랍니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
finally 블럭은 예외의 발생여부에 상관없이 실행되어야할 코드를 포함시킬 목적으로 사용됩니다. try-catch문의 끝에 선택적으로 덧붙여 사용할 수 있으며, try-catch-finally의 순서로 구성됩니다.
________________________________________________________________________________________________________________________________________________________________________
finally 블럭을 사용하지 않은 사례는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-17
```java
class FinallyTest {
    public static void main(String args[]) {
        try {
            startInstall();		// 프로그램 설치에 필요한 준비를 한다.
            copyFiles();		// 파일들을 복사한다.
            deleteTempFiles();	// 프로그램 설치에 사용된 임시파일들을 삭제한다.
        } catch (Exception e) {
            e.printStackTrace();
            deleteTempFiles();   // 프로그램 설치에 사용된 임시파일들을 삭제한다.
        } // try-catch의 끝
    } // main의 끝

    static void startInstall() {
        /* 프로그램 설치에 필요한 준비를 하는 코드를 적는다.*/
    }
    static void copyFiles() { /* 파일들을 복사하는 코드를 적는다. */ }
    static void deleteTempFiles() { /* 임시파일들을 삭제하는 코드를 적는다.*/ }
}
```
________________________________________________________________________________________________________________________________________________________________________
예제 8-17을 finally 블럭을 활용하여 변형한 예제는 다음과 같다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-18
```java
class FinallyTest2 {
    public static void main(String args[]) {
        try {
            startInstall();		// 프로그램 설치에 필요한 준비를 한다.
            copyFiles();		// 파일들을 복사한다.
        } catch (Exception e)	{
            e.printStackTrace();
        } finally {
            deleteTempFiles();	// 프로그램 설치에 사용된 임시파일들을 삭제한다.
        } // try-catch의 끝
    }	// main의 끝

    static void startInstall() {
        /* 프로그램 설치에 필요한 준비를 하는 코드를 적는다.,*/
    }
    static void copyFiles() { /* 파일들을 복사하는 코드를 적는다. */ }
    static void deleteTempFiles() { /* 임시파일들을 삭제하는 코드를 적는다.*/}
}
```
________________________________________________________________________________________________________________________________________________________________________
예제 8-19는 finally 블럭을 활용하여 메서드1을 실행시키는 예시입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-19
```java
class FinallyTest3 {
    public static void main(String args[]) {
        // method1()은 static메서드이므로 인스턴스 생성없이 직접 호출이 가능하다.
        FinallyTest3.method1();
        System.out.println("method1()의 수행을 마치고 main메서드로 돌아왔습니다.");
    }	// main메서드의 끝

    static void method1() {
        try {
            System.out.println("method1()이 호출되었습니다.");
            return;		// 현재 실행 중인 메서드를 종료한다.
        }	catch (Exception e)	{
            e.printStackTrace();
        } finally {
            System.out.println("method1()의 finally블럭이 실행되었습니다.");
        }
    }	// method1메서드의 끝
}
```
```
method1()이 호출되었습니다.
method1()의 finally블럭이 실행되었습니다.
method1()의 수행을 마치고 main메서드로 돌아왔습니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
자동 자원 반환문인 try-with-resource문을 활용한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-20
```java
class TryWithResourceEx {
    public static void main(String args[]) {

        try (CloseableResource cr = new CloseableResource()) {
            cr.exceptionWork(false); // 예외가 발생하지 않는다.
        } catch(WorkException e) {
            e.printStackTrace();
        } catch(CloseException e) {
            e.printStackTrace();
        }
        System.out.println();

        try (CloseableResource cr = new CloseableResource()) {
            cr.exceptionWork(true); // 예외가 발생한다.
        } catch(WorkException e) {
            e.printStackTrace();
        } catch(CloseException e) {
            e.printStackTrace();
        }
    } // main의 끝
}

class CloseableResource implements AutoCloseable {
    public void exceptionWork(boolean exception) throws WorkException {
        System.out.println("exceptionWork("+exception+")가 호출됨");

        if(exception)
            throw new WorkException("WorkException발생!!!");
    }

    public void close() throws CloseException {
        System.out.println("close()가 호출됨");
        throw new CloseException("CloseException발생!!!");
    }
}

class WorkException extends Exception {
    WorkException(String msg) { super(msg); }
}

class CloseException extends Exception {
    CloseException(String msg) { super(msg); }
}
```
```
exceptionWork(false)가 호출됨
close()가 호출됨

exceptionWork(true)가 호출됨
close()가 호출됨
CloseException: CloseException발생!!!
	at CloseableResource.close(HelloWorldPrint.java:33)
	at TryWithResourceEx.main(HelloWorldPrint.java:6)
WorkException: WorkException발생!!!
	at CloseableResource.exceptionWork(HelloWorldPrint.java:28)
	at TryWithResourceEx.main(HelloWorldPrint.java:14)
	Suppressed: CloseException: CloseException발생!!!
		at CloseableResource.close(HelloWorldPrint.java:33)
		at TryWithResourceEx.main(HelloWorldPrint.java:15)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
사용자정의 예외를 만드는 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-21
```java
class NewExceptionTest {
    public static void main(String args[]) {
        try {
            startInstall();		// 프로그램 설치에 필요한 준비를 한다.
            copyFiles();			// 파일들을 복사한다.
        } catch (SpaceException e)	{
            System.out.println("에러 메시지 : " + e.getMessage());
            e.printStackTrace();
            System.out.println("공간을 확보한 후에 다시 설치하시기 바랍니다.");
        } catch (MemoryException me)	{
            System.out.println("에러 메시지 : " + me.getMessage());
            me.printStackTrace();
            System.gc();		//  Garbage Collection을 수행하여 메모리를 눌러준다.
            System.out.println("다시 설치를 시도하세요.");
        } finally {
            deleteTempFiles();	// 프로그램 설치에 사용된 임시파일들을 삭제한다.
        } // try의 끝
    } // main의 끝

    static void startInstall() throws SpaceException, MemoryException {
        if(!enoughSpace()) 		// 충분히 설치 공간이 없으면...
            throw new SpaceException("설치할 공간이 부족합니다.");
        if (!enoughMemory())		// 충분한 메모리가 없으면...
            throw new MemoryException("메모리가 부족합니다.");
    } // startInstall메서드의 끝

    static void copyFiles() { /* 파일들을 복사하는 코드를 적는다. */ }
    static void deleteTempFiles() { /* 임시파일들을 삭제하는 코드를 적는다.*/}

    static boolean enoughSpace()   {
        // 설치하는데 필요한 공간이 있는지 확인하는 코드를 적는다.
        return false;
    }
    static boolean enoughMemory() {
        // 설치하는데 필요한 메모리공간이 있는지 확인하는 코드를 적는다.
        return true;
    }
} // ExceptionTest클래스의 끝

class SpaceException extends Exception {
    SpaceException(String msg) {
        super(msg);
    }
}

class MemoryException extends Exception {
    MemoryException(String msg) {
        super(msg);
    }
}
```
```
에러 메시지 : 설치할 공간이 부족합니다.
공간을 확보한 후에 다시 설치하시기 바랍니다.
SpaceException: 설치할 공간이 부족합니다.
	at NewExceptionTest.startInstall(HelloWorldPrint.java:22)
	at NewExceptionTest.main(HelloWorldPrint.java:4)

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예외를 되던지기를 진행한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-22
```java
class ExceptionEx17 {
    public static void main(String[] args)
    {
        try  {
            method1();
        } catch (Exception e)	{
            System.out.println("main메서드에서 예외가 처리되었습니다.");
        }
    }	// main메서드의 끝

    static void method1() throws Exception {
        try {
            throw new Exception();
        } catch (Exception e) {
            System.out.println("method1메서드에서 예외가 처리되었습니다.");
            throw e;			// 다시 예외를 발생시킨다.
        }
    }	// method1메서드의 끝
}
```
```
method1메서드에서 예외가 처리되었습니다.
main메서드에서 예외가 처리되었습니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
위의 예제들에서 적용된 기법들과 연결된 예외를 모두 적용한 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 8-23
```java
class ChainedExceptionEx {
    public static void main(String args[]) {
        try {
            install();
        } catch(InstallException e) {
            e.printStackTrace();
        } catch(Exception e) {
            e.printStackTrace();
        }
    } // main의 끝

    static void install() throws InstallException {
        try {
            startInstall();		// 프로그램 설치에 필요한 준비를 한다.
            copyFiles();		// 파일들을 복사한다.
        } catch (SpaceException e)	{
            InstallException ie = new InstallException("설치 중 예외발생");
            ie.initCause(e);
            throw ie;
        } catch (MemoryException me) {
            InstallException ie = new InstallException("설치 중 예외발생");
            ie.initCause(me);
            throw ie;
        } finally {
            deleteTempFiles();	// 프로그램 설치에 사용된 임시파일들을 삭제한다.
        } // try의 끝
    }

    static void startInstall() throws SpaceException, MemoryException {
        if(!enoughSpace()) { 		// 충분한 설치 공간이 없으면...
            throw new SpaceException("설치할 공간이 부족합니다.");
        }

        if (!enoughMemory()) {		// 충분한 메모리가 없으면...
            throw new MemoryException("메모리가 부족합니다.");
//		throw new RuntimeException(new MemoryException("메모리가 부족합니다."));
        }
    } // startInstall메서드의 끝

    static void copyFiles() { /* 파일들을 복사하는 코드를 적는다. */ }
    static void deleteTempFiles() { /* 임시파일들을 삭제하는 코드를 적는다.*/}

    static boolean enoughSpace()   {
        // 설치하는데 필요한 공간이 있는지 확인하는 코드를 적는다.
        return false;
    }
    static boolean enoughMemory() {
        // 설치하는데 필요한 메모리공간이 있는지 확인하는 코드를 적는다.
        return true;
    }
} // ExceptionTest클래스의 끝

class InstallException extends Exception {
    InstallException(String msg) {
        super(msg);
    }
}

class SpaceException extends Exception {
    SpaceException(String msg) {
        super(msg);
    }
}

class MemoryException extends Exception {
    MemoryException(String msg) {
        super(msg);
    }
}
```
```
InstallException: 설치 중 예외발생
	at ChainedExceptionEx.install(HelloWorldPrint.java:17)
	at ChainedExceptionEx.main(HelloWorldPrint.java:4)
Caused by: SpaceException: 설치할 공간이 부족합니다.
	at ChainedExceptionEx.startInstall(HelloWorldPrint.java:31)
	at ChainedExceptionEx.install(HelloWorldPrint.java:14)
	... 1 more

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
여기까지가 예외처리에 관한 문서 내에 수록된 예제입니다. 다음 포스팅에서는 java.lang 패키지와 유용한 클래스에 대해 알아보도록 하겠습니다. 오늘도 긴 글 읽어주시느라 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=