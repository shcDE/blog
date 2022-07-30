---
title: "자바 공부 기록(11) - 입출력 I/O에 대해 알아볼까요?"
date: 2022-07-30T14:46:34+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅에서는 자바에서의 입출력 I/O에 대해 알아보겠습니다. I/O란 Input과 Output의 약자로 입력과 출력, 간단히 줄여서 입출력이라고 불립니다. 이러한 입출력은 컴퓨터 내부 또는 외부의 장치와 프로그램 간의 데이터를 주고받는 것을 의미합니다. 이번 포스팅을 끝으로 기본 단계에서의 자바 포스팅을 마무리하고자 합니다. 사실 제가 공부하는 책에서는 진도가 4장이 더 있지만, 문서에 따르면 나머지 파트는 프로젝트를 진행하다가 필요한 경우에 보는 것을 권장해서 그 진도 그대로 따라갔기 때문에 스킵하였습니다. 그래서 나중에 프로젝트를 하다가 필요한 경우가 발생하면 그 때 포스팅할 예정입니다. 마지막 포스팅까지 따라와 주셔서 감사하고, 그러면 바이트 기반 스트림부터 차근차근 알아보도록 하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 15장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
________________________________________________________________________________________________________________________________________________________________________
바이트 기반 스트림에서 ByteArrayInputStream과 ByteArrayOutputStream은 메모리, 즉 바이트 배열에 데이터를 입출력하는데 사용되는 스트림입니다. 주로 다른 곳에 입출력하기 전에 데이터를 임시로 바이트 배열에 담아서 변환 등의 작업을 하는데 사용됩니다. 관련된 예제는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-1
```java
import java.io.*;
import java.util.Arrays;

class IOEx1 {
    public static void main(String[] args) {
        byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
        byte[] outSrc = null;

        ByteArrayInputStream  input  = null;
        ByteArrayOutputStream output = null;

        input  = new ByteArrayInputStream(inSrc);
        output = new ByteArrayOutputStream();

        int data = 0;

        while((data = input.read())!=-1) {
            output.write(data);	// void write(int b)
        }

        outSrc = output.toByteArray(); // 스트림의 내용을 byte배열로 반환한다.

        System.out.println("Input Source  :" + Arrays.toString(inSrc));
        System.out.println("Output Source :" + Arrays.toString(outSrc));
    }
}
```
```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-2
```java
import java.io.*;
import java.util.Arrays;

class IOEx2 {
    public static void main(String[] args) {
        byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
        byte[] outSrc = null;

        byte[] temp = new byte[10];

        ByteArrayInputStream  input  = null;
        ByteArrayOutputStream output = null;

        input  = new ByteArrayInputStream(inSrc);
        output = new ByteArrayOutputStream();

        input.read(temp,0,temp.length); // 읽어온 데이터를 배열 temp에 담는다.
        output.write(temp,5, 5);	    // temp[5]부터 5개의 데이터를 write한다.

        outSrc = output.toByteArray();

        System.out.println("Input Source  :" + Arrays.toString(inSrc));
        System.out.println("temp          :" + Arrays.toString(temp));
        System.out.println("Output Source :" + Arrays.toString(outSrc));

    }
}
```
```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
temp          :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
Output Source :[5, 6, 7, 8, 9]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-3
```java
import java.io.*;
import java.util.Arrays;

class IOEx3 {
    public static void main(String[] args) {
        byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
        byte[] outSrc = null;

        byte[] temp = new byte[4];	// 이전 예제와 배열의 크기가 다르다.

        ByteArrayInputStream input = null;
        ByteArrayOutputStream output = null;

        input  = new ByteArrayInputStream(inSrc);
        output = new ByteArrayOutputStream();

        System.out.println("Input Source  :" + Arrays.toString(inSrc));

        try {
            while(input.available() > 0) {
                input.read(temp);
                output.write(temp);
//				System.out.println("temp :" + Arrays.toString(temp));

                outSrc = output.toByteArray();
                printArrays(temp, outSrc);
            }
        } catch(IOException e) {}
    } // main의 끝

    static void printArrays(byte[] temp, byte[] outSrc) {
        System.out.println("temp          :" +Arrays.toString(temp));
        System.out.println("Output Source :" +Arrays.toString(outSrc));
    }
}
```
```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
temp          :[0, 1, 2, 3]
Output Source :[0, 1, 2, 3]
temp          :[4, 5, 6, 7]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7]
temp          :[8, 9, 6, 7]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 6, 7]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-4
```java
import java.io.*;
import java.util.Arrays;

class IOEx4 {
    public static void main(String[] args) {
        byte[] inSrc = {0,1,2,3,4,5,6,7,8,9};
        byte[] outSrc = null;

        byte[] temp = new byte[4];

        ByteArrayInputStream  input  = null;
        ByteArrayOutputStream output = null;

        input  = new ByteArrayInputStream(inSrc);
        output = new ByteArrayOutputStream();

        try {
            while(input.available() > 0) {
                int len = input.read(temp); // 읽어온 데이터의 개수를 반환한다.
                output.write(temp, 0, len); // 읽어온 만큼만 write한다.
            }
        } catch(IOException e) {}

        outSrc = output.toByteArray();

        System.out.println("Input Source  :" + Arrays.toString(inSrc));
        System.out.println("temp          :" + Arrays.toString(temp));
        System.out.println("Output Source :" + Arrays.toString(outSrc));
    }
}
```
```
Input Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
temp          :[8, 9, 6, 7]
Output Source :[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
DataInputStream과 DataOutputStream에 대한 예시는 다음과 같이 6개가 있습니다. 이중에서 3개만 보여드리겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-8
```java
import java.io.*;

class DataOutputStreamEx1 {
    public static void main(String args[]) {
        FileOutputStream fos = null;
        DataOutputStream dos = null;

        try {
            fos = new FileOutputStream("sample.dat");
            dos = new DataOutputStream(fos);
            dos.writeInt(10);
            dos.writeFloat(20.0f);
            dos.writeBoolean(true);

            dos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    } // main
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-9
```java
import java.io.*;
import java.util.Arrays;

class DataOutputStreamEx2 {
    public static void main(String args[]) {
        ByteArrayOutputStream bos = null;
        DataOutputStream      dos = null;

        byte[] result = null;

        try {
            bos = new ByteArrayOutputStream();
            dos = new DataOutputStream(bos);
            dos.writeInt(10);
            dos.writeFloat(20.0f);
            dos.writeBoolean(true);

            result = bos.toByteArray();

            String[] hex = new String[result.length];

            for(int i=0;i<result.length; i++) {
                if(result[i] < 0) {
                    hex[i] = String.format("%02x", result[i]+256);
                } else {
                    hex[i] = String.format("%02x", result[i]);
                }
            }

            System.out.println("10진수 :" + Arrays.toString(result));
            System.out.println("16진수 :" + Arrays.toString(hex));

            dos.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    } // main
}
```
```
10진수 :[0, 0, 0, 10, 65, -96, 0, 0, 1]
16진수 :[00, 00, 00, 0a, 41, a0, 00, 00, 01]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-10
```java
import java.io.*;

class DataInputStreamEx1 {
    public static void main(String args[]) {
        try {
            FileInputStream fis = new FileInputStream("sample.dat");
            DataInputStream dis = new DataInputStream(fis);

            System.out.println(dis.readInt());
            System.out.println(dis.readFloat());
            System.out.println(dis.readBoolean());
            dis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    } // main
}
```
```
10
20.0
true

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
SequenceInputStream에 대한 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-14
```java
import java.io.*;
import java.util.*;

class SequenceInputStreamEx {
    public static void main(String[] args) {
        byte[] arr1 = {0,1,2};
        byte[] arr2 = {3,4,5};
        byte[] arr3 = {6,7,8};
        byte[] outSrc = null;

        Vector v = new Vector();
        v.add(new ByteArrayInputStream(arr1));
        v.add(new ByteArrayInputStream(arr2));
        v.add(new ByteArrayInputStream(arr3));

        SequenceInputStream   input  = new SequenceInputStream(v.elements());
        ByteArrayOutputStream output = new ByteArrayOutputStream();

        int data = 0;

        try {
            while((data = input.read())!=-1) {
                output.write(data);	// void write(int b)
            }
        } catch(IOException e) {}

        outSrc = output.toByteArray();

        System.out.println("Input Source1  :" + Arrays.toString(arr1));
        System.out.println("Input Source2  :" + Arrays.toString(arr2));
        System.out.println("Input Source3  :" + Arrays.toString(arr3));
        System.out.println("Output Source  :" + Arrays.toString(outSrc));
    }
}
```
```
Input Source1  :[0, 1, 2]
Input Source2  :[3, 4, 5]
Input Source3  :[6, 7, 8]
Output Source  :[0, 1, 2, 3, 4, 5, 6, 7, 8]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
PrintStream에 대한 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-15
```java
import java.util.Date;

class PrintStreamEx1 {
    public static void main(String[] args) {
        int    i = 65;
        float f = 1234.56789f;

        Date d = new Date();

        System.out.printf("문자 %c의 코드는 %d%n", i, i);
        System.out.printf("%d는 8진수로 %o, 16진수로 %x%n", i ,i, i);
        System.out.printf("%3d%3d%3d%n", 100, 90, 80);
        System.out.println();
        System.out.printf("123456789012345678901234567890%n");
        System.out.printf("%s%-5s%5s%n", "123", "123", "123");
        System.out.println();
        System.out.printf("%-8.1f%8.1f %e%n",f,f,f);
        System.out.println();
        System.out.printf("오늘은 %tY년 %tm월 %td일입니다. %n", d,d,d );
        System.out.printf("지금은 %tH시 %tM분 %tS초입니다. %n", d,d,d );
        System.out.printf("지금은 %1$tH시 %1$tM분 %1$tS초입니다. %n", d );
    }
}
```
```
문자 A의 코드는 65
65는 8진수로 101, 16진수로 41
100 90 80

123456789012345678901234567890
123123    123

1234.6    1234.6 1.234568e+03

오늘은 2022년 07월 30일입니다. 
지금은 16시 14분 38초입니다. 
지금은 16시 14분 38초입니다. 

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
이제 문자기반 스트림에 대한 예제를 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
PipedReader와 PipedWriter에 대한 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-18
```java
import java.io.*;

public class HelloWorldPrint {
    public static void main(String args[]) {
        InputThread   inThread = new InputThread("InputThread");
        OutputThread outThread = new OutputThread("OutputThread");

        //PipedReader와 PipedWriter를 연결한다.
        inThread.connect(outThread.getOutput());

        inThread.start();
        outThread.start();
    } // main
}

class InputThread extends Thread {
    PipedReader  input = new PipedReader();
    StringWriter sw    = new StringWriter();

    InputThread(String name) {
        super(name);		// Thread(String name);
    }

    public void run() {
        try {
            int data = 0;

            while((data=input.read()) != -1) {
                sw.write(data);
            }
            System.out.println(getName() + " received : " + sw.toString());
        } catch(IOException e) {}
    } // run

    public PipedReader getInput() {
        return input;
    }

    public void connect(PipedWriter output) {
        try {
            input.connect(output);
        } catch(IOException e) {}
    } // connect
}

class OutputThread extends Thread {
    PipedWriter output = new PipedWriter();

    OutputThread(String name) {
        super(name);		// Thread(String name);
    }

    public void run() {
        try {
            String msg = "Hello";
            System.out.println(getName() + " sent : " + msg);
            output.write(msg);
            output.close();
        } catch(IOException e) {}
    } // run

    public PipedWriter getOutput() {
        return output;
    }

    public void connect(PipedReader input) {
        try {
            output.connect(input);
        } catch(IOException e) {}
    } // connect
}
```
```
OutputThread sent : Hello
InputThread received : Hello

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
StringReader와 StringWriter에 대한 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-19
```java
import java.io.*;

class StringReaderWriterEx {
    public static void main(String[] args) {
        String inputData = "ABCD";
        StringReader input  = new StringReader(inputData);
        StringWriter output = new StringWriter();

        int data = 0;

        try {
            while((data = input.read())!=-1) {
                output.write(data);	// void write(int b)
            }
        } catch(IOException e) {}

        System.out.println("Input Data  :" + inputData);
        System.out.println("Output Data :" + output.toString());
//		System.out.println("Output Data :" + output.getBuffer().toString());
    }
}
```
```
Input Data  :ABCD
Output Data :ABCD

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
RandomAccessFile에 대한 예제는 다음과 같이 3개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-25
```java
import java.io.*;

class RandomAccessFileEx1 {
    public static void main(String[] args) {
        try {
            RandomAccessFile raf = new RandomAccessFile("test.dat", "rw");
            System.out.println("파일 포인터의 위치: " + raf.getFilePointer());
            raf.writeInt(100);
            System.out.println("파일 포인터의 위치: " + raf.getFilePointer());
            raf.writeLong(100L);
            System.out.println("파일 포인터의 위치: " + raf.getFilePointer());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
```
파일 포인터의 위치: 0
파일 포인터의 위치: 4
파일 포인터의 위치: 12

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-26
```java
import java.io.*;

class RandomAccessFileEx2 {
    public static void main(String args[]) {
//				      번호, 국어, 영어, 수학
        int[] score = {	1, 100,  90,  90,
                2,  70,  90, 100,
                3, 100, 100, 100,
                4,  70,  60,  80,
                5,  70,  90, 100
        };

        try {
            RandomAccessFile raf = new RandomAccessFile("score2.dat", "rw");

            for(int i=0; i<score.length;i++) {
                raf.writeInt(score[i]);
            }

            while(true) {
                System.out.println(raf.readInt());
            }
        } catch (EOFException eof) {
            // readInt()를 호출했을 때 더 이상 읽을 내용이 없으면 EOFException이 발생한다.
        } catch (IOException e) {
            e.printStackTrace();
        }
    } // main
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-27
```java
import java.io.*;

class RandomAccessFileEx3 {
    public static void main(String args[]) {
        int sum = 0;

        try {
            RandomAccessFile raf = new RandomAccessFile("score2.dat", "r");
            int i=4;

            while(true) {
                raf.seek(i);
                sum += raf.readInt();
                i+=16;
            }
        } catch (EOFException e) {
            System.out.println("sum : " + sum);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
```
sum : 410

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
File과 관련된 11개의 예제는 윈도우 OS 기준으로 작성되어 있어 나중에 맥 OS 환경에서 사용하는 법을 익힐 예정입니다.
________________________________________________________________________________________________________________________________________________________________________
다음으로 직렬화에 대해 알아보겠습니다. 직렬화란 객체를 데이터 스트림으로 만드는 것을 의미합니다. 다시 얘기하면 객체에 저장된 데이터를 스트림에 쓰기 위해 연속적인 데이터로 변환하는 것을 의미합니다. 반대로 스트림으로부터 데이터를 읽어서 객체를 만드는 것을 역직렬화라고 합니다. 이러한 직렬화에 관련된 예제를 살펴보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
Serializable, transient와 관련된 예제는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-39
```java
public class UserInfo implements java.io.Serializable {
    String name;
    String password;
    int age;

    public UserInfo() {
        this("Unknown", "1111", 0);
    }

    public UserInfo(String name, String password, int age) {
        this.name = name;
        this.password = password;
        this.age = age;
    }

    public String toString() {
        return "("+ name + "," + password + "," + age + ")";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-40
```java
import java.io.*;
import java.util.ArrayList;

public class SerialEx1 {
    public static void main(String[] args) {
        try {
            String fileName = "UserInfo.ser";
            FileOutputStream     fos = new FileOutputStream(fileName);
            BufferedOutputStream bos = new BufferedOutputStream(fos);

            ObjectOutputStream out = new ObjectOutputStream(bos);

            UserInfo u1 = new UserInfo("JavaMan","1234",30);
            UserInfo u2 = new UserInfo("JavaWoman","4321",26);

            ArrayList<UserInfo> list = new ArrayList<>();
            list.add(u1);
            list.add(u2);

            // 객체를 직렬화한다.
            out.writeObject(u1);
            out.writeObject(u2);
            out.writeObject(list);
            out.close();
            System.out.println("직렬화가 잘 끝났습니다.");
        } catch(IOException e) {
            e.printStackTrace();
        }
    } // main
} // class
```
```
직렬화가 잘 끝났습니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-41
```java
import java.io.*;
import java.util.ArrayList;

public class SerialEx2 {
    public static void main(String[] args) {
        try {
            String fileName = "UserInfo.ser";
            FileInputStream     fis = new FileInputStream(fileName);
            BufferedInputStream bis = new BufferedInputStream(fis);

            ObjectInputStream in = new ObjectInputStream(bis);

            // 객체를 읽을 때는 출력한 순서와 일치해야한다.
            UserInfo u1 = (UserInfo)in.readObject();
            UserInfo u2 = (UserInfo)in.readObject();
            ArrayList list = (ArrayList)in.readObject();

            System.out.println(u1);
            System.out.println(u2);
            System.out.println(list);
            in.close();
        } catch(Exception e) {
            e.printStackTrace();
        }
    } // main
} // class
```
```
(JavaMan,1234,30)
(JavaWoman,4321,26)
[(JavaMan,1234,30), (JavaWoman,4321,26)]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 15-42
```java
import java.io.*;

class SuperUserInfo {
	String name;
	String password;

	SuperUserInfo() {
		this("Unknown","1111");
	}

	SuperUserInfo(String name, String password) {
		this.name = name;
		this.password = password;
	}
} // class SuperUserInfo

public class UserInfo2 extends SuperUserInfo implements java.io.Serializable {
	int age;

	public UserInfo2() {
		this("Unknown", "1111", 0);
	}

	public UserInfo2(String name, String password, int age) {
		super(name, password);
		this.age = age;	
	}

	public String toString() {
		return "("+ name + "," + password + "," + age + ")";		
	}

	private void writeObject(ObjectOutputStream out)
		throws IOException {
		out.writeUTF(name);	
		out.writeUTF(password);	
		out.defaultWriteObject();
	}

	private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
		name = in.readUTF();
		password = in.readUTF();
		in.defaultReadObject();
	
	}
} // class UserInfo2 
```
________________________________________________________________________________________________________________________________________________________________________
이렇게 입출력 I/O와 관련된 핵심적인 예제를 확인했습니다. 이번 포스팅에서 제가 실행하기 힘든 부분은 꽤 많이 건드리지 못한 것을 느낍니다. 그래서 자바 심화 공부를 추후에 진행할 때 입출력 파트도 다시 같이 공부할 예정입니다. 이번 포스팅을 마지막으로 자바 기본 공부 기록을 마무리하게 되었습니다. 11편의 포스팅 동안 꾸준히 읽어주신 분들께 모두 감사드리고 즐거운 주말 되시길 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=