---
title: "자바 공부 기록(9) - 컬렉션 프레임워크에 대해 알아볼까요?"
date: 2022-07-28T14:05:43+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅에서는 컬렉션 프레임워크에 대해 알아보겠습니다. 컬렉션 프레임워크란 '데이터 군을 저장하는 클래스들을 표준화한 설계'를 의밓합니다. 컬렉션은 다수의 데이터, 즉 데이터 그룹을, 프레임워크는 표준화된 프로그래밍 방식을 의미합니다.
________________________________________________________________________________________________________________________________________________________________________
이러한 컬렉션 프레임워크는 컬렉션을 다루는데 필요한 다양하고 풍부한 클래스들을 제공하기 때문에 프로그래머의 짐을 상당히 덜어주고 있으며, 또한 인터페이스와 다형성을 이용한 객체지향적 설계를 통해 표준화되어 있기 때문에 사용법을 익히기에도 편리하고 재사용성이 높은 코드를 작성할 수 있다는 장점이 있습니다. 지금부터 ArrayList부터 하나씩 차근차근 컬렉션 프레임워크에 대해 알아보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 직접 ['자바의 정석(3판)'](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=)을 구매하여 11장을 공부한 내용을 정리한 자료임을 밝힙니다. 추가로 사용한 IDE는 '인텔리제이 IDEA CE'이며, 자바8 버전을 사용하였음을 밝힙니다.
________________________________________________________________________________________________________________________________________________________________________
ArrayList란 컬렉션 프레임워크에서 가장 많이 사용되는 컬렉션 클래스입니다. 이러한 ArrayList는 기존의 Vector를 개선한 것으로 Vector의 구현원리와 기능적인 측면에서 동일하다고 할 수 있습니다. 이러한 Vector는 기존에 작성된 소스와의 호환성을 위해서 계속 남겨 두고 있을 뿐이기 때문에 가능하면 Vector보다는 ArrayList를 사용하는 것을 권장합니다. 지금부터 ArrayList와 관련된 4개의 예제를 살펴보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-1
```java
import java.util.*;

class ArrayListEx1{
    public static void main(String[] args) {
        ArrayList list1 = new ArrayList(10);
        list1.add(new Integer(5));
        list1.add(new Integer(4));
        list1.add(new Integer(2));
        list1.add(new Integer(0));
        list1.add(new Integer(1));
        list1.add(new Integer(3));

        ArrayList list2 = new ArrayList(list1.subList(1,4));
        print(list1, list2);

        Collections.sort(list1);	// list1과 list2를 정렬한다.
        Collections.sort(list2);	// Collections.sort(List l)
        print(list1, list2);

        System.out.println("list1.containsAll(list2):" + list1.containsAll(list2));

        list2.add("B");
        list2.add("C");
        list2.add(3, "A");
        print(list1, list2);

        list2.set(3, "AA");
        print(list1, list2);

        // list1에서 list2와 겹치는 부분만 남기고 나머지는 삭제한다.
        System.out.println("list1.retainAll(list2):" + list1.retainAll(list2));
        print(list1, list2);

        //  list2에서 list1에 포함된 객체들을 삭제한다.
        for(int i= list2.size()-1; i >= 0; i--) {
            if(list1.contains(list2.get(i)))
                list2.remove(i);
        }
        print(list1, list2);
    } // main의 끝

    static void print(ArrayList list1, ArrayList list2) {
        System.out.println("list1:"+list1);
        System.out.println("list2:"+list2);
        System.out.println();
    }
} // class
```
```
list1:[5, 4, 2, 0, 1, 3]
list2:[4, 2, 0]

list1:[0, 1, 2, 3, 4, 5]
list2:[0, 2, 4]

list1.containsAll(list2):true
list1:[0, 1, 2, 3, 4, 5]
list2:[0, 2, 4, A, B, C]

list1:[0, 1, 2, 3, 4, 5]
list2:[0, 2, 4, AA, B, C]

list1.retainAll(list2):true
list1:[0, 2, 4]
list2:[0, 2, 4, AA, B, C]

list1:[0, 2, 4]
list2:[AA, B, C]


Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-2
```java
import java.util.*;

class ArrayListEx2 {
    public static void main(String[] args) {
        final int LIMIT = 10;	// 자르고자 하는 글자의 개수를 지정한다.
        String source = "0123456789abcdefghijABCDEFGHIJ!@#$%^&*()ZZZ";
        int length = source.length();

        List list = new ArrayList(length/LIMIT + 10); // 크기를 약간 여유 있게 잡는다.

        for(int i=0; i < length; i+=LIMIT) {
            if(i+LIMIT < length )
                list.add(source.substring(i, i+LIMIT));
            else
                list.add(source.substring(i));
        }

        for(int i=0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
    } // main()
} 
```
```
0123456789
abcdefghij
ABCDEFGHIJ
!@#$%^&*()
ZZZ

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음 예제는 Vector와 ArrayList를 비교하기 위해 보여주는 예시입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-3
```java
import java.util.*;

class VectorEx1 {
    public static void main(String[] args) {
        Vector v = new Vector(5);	// 용량(capacity)이 5인 Vector를 생성한다.
        v.add("1");
        v.add("2");
        v.add("3");
        print(v);

        v.trimToSize();	// 빈 공간을 없앤다. (용량과 크기가 같아진다.)
        System.out.println("=== After trimToSize() ===");
        print(v);

        v.ensureCapacity(6);
        System.out.println("=== After ensureCapacity(6) ===");
        print(v);

        v.setSize(7);
        System.out.println("=== After setSize(7) ===");
        print(v);

        v.clear();
        System.out.println("=== After clear() ===");
        print(v);
    }

    public static void print(Vector v) {
        System.out.println(v);
        System.out.println("size :" + v.size());
        System.out.println("capacity :" + v.capacity());
    }
}
```
```
[1, 2, 3]
size :3
capacity :5
=== After trimToSize() ===
[1, 2, 3]
size :3
capacity :3
=== After ensureCapacity(6) ===
[1, 2, 3]
size :3
capacity :6
=== After setSize(7) ===
[1, 2, 3, null, null, null, null]
size :7
capacity :12
=== After clear() ===
[]
size :0
capacity :12

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음 예제는 Vector클래스의 실제 코드를 바탕으로 이해하기 위해 쉽게 재구성한 예시입니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-4
```java
import java.util.*;

public class MyVector implements List {
    Object[] data = null;	// 객체를 담기 위한 객체배열을 선언한다.
    int capacity = 0;		// 용량
    int size = 0;			// 크기

    public MyVector(int capacity) {
        if (capacity < 0)
            throw new IllegalArgumentException("유효하지 않은 값입니다. :"+ capacity);

        this.capacity = capacity;
        data = new Object[capacity];
    }

    public MyVector() {
        this(10);		// 크기를 지정하지 않으면 크기를 10으로 한다.
    }

    // 최소한의 저장공간(capacity)를 확보하는 메서드
    public void ensureCapacity(int minCapacity) {
        if (minCapacity - data.length > 0)
            setCapacity(minCapacity);
    }

    public boolean add(Object obj) {
        // 새로운 객체를 저장하기 전에 저장할 공간을 확보한다.
        ensureCapacity(size+1);
        data[size++] = obj;
        return true;
    }

    public Object get(int index) {
        if(index < 0 || index >= size)
            throw new IndexOutOfBoundsException("범위를 벗어났습니다.");

        return data[index];
    }

    public Object remove(int index) {
        Object oldObj = null;

        if(index < 0 || index >= size)
            throw new IndexOutOfBoundsException("범위를 벗어났습니다.");

        oldObj = data[index];

        // 삭제하고자 하는 객체가 마지막 객체가 아니라면, 배열 복사를 통해 빈자리를 채워줘야 한다.
        if(index != size-1) {
            System.arraycopy(data, index+1, data, index, size-index-1);
        }

        // 마지막 데이터를 null로 한다. 배열은 0부터 시작하므로 마지막 요소는 index가 size-1이다.
        data[size-1] = null;
        size--;
        return oldObj;
    }

    public boolean remove(Object obj) {
        for(int i=0; i< size; i++) {
            if(obj.equals(data[i])) {
                remove(i);
                return true;
            }
        }
        return false;
    }

    public void trimToSize() {
        setCapacity(size);
    }

    private void setCapacity(int capacity) {
        if(this.capacity==capacity) return; // 크기가 같으면 변경하지 않는다.

        Object[] tmp = new Object[capacity];
        System.arraycopy(data,0, tmp, 0, size);
        data = tmp;
        this.capacity = capacity;
    }

    public void clear(){
        for (int i = 0; i < size; i++)
            data[i] = null;
        size = 0;
    }

    public Object[] toArray(){
        Object[] result = new Object[size];
        System.arraycopy(data, 0, result, 0, size);

        return result;
    }

    public boolean isEmpty() { return size==0;}
    public int capacity() { return capacity; }
    public int size() { return size; }
/****************************************/
    /*       List인터페이스로부터 상속받은 메서드들         */
    /****************************************/
//  public int size();
//  public boolean isEmpty();
    public boolean contains(Object o){ return false;}
    public Iterator iterator(){ return null; }
    //	public Object[] toArray();
    public Object[] toArray(Object a[]){ return null;}
    //  public boolean add(Object o);
//  public boolean remove(Object o);
    public boolean containsAll(Collection c){ return false; }
    public boolean addAll(Collection c){ return false; }
    public boolean addAll(int index, Collection c){ return false; }
    public boolean removeAll(Collection c){ return false; }
    public boolean retainAll(Collection c){ return false; }
    //	public void clear();
    public boolean equals(Object o){ return false; }
    //	public int hashCode();
//  public Object get(int index);
    public Object set(int index, Object element){ return null;}
    public void add(int index, Object element){}
    //  public Object remove(int index);
    public int indexOf(Object o){ return -1;}
    public int lastIndexOf(Object o){ return -1;}
    public ListIterator listIterator(){ return null; }
    public ListIterator listIterator(int index){ return null; }
    public List subList(int fromIndex, int toIndex){ return null; }

    default void sort(Comparator c) { /* 내용생략 */ }                     // JDK1.8부터
    default Spliterator spliterator() { /* 내용생략 */ }                  // JDK1.8부터
    default void replaceAll(UnaryOperator operator){/* 내용생략 */} //JDK1.8부터
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 LinkedList에 대해 알아보겠습니다. LinkedList란 불연속적으로 존재하는 데이터를 서로 연결한 형태로 구성되어 있는 자료구조의 일종입니다. 이러한 LinkedList와 관련된 예제는 다음과 같이 2개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-5
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String args[]) {
        // 추가할 데이터의 개수를 고려하여 충분히 잡아야 한다.
        ArrayList al = new ArrayList(2000000);
        LinkedList ll = new LinkedList();

        System.out.println("= 순차적으로 추가하기 =");
        System.out.println("ArrayList :"+add1(al));
        System.out.println("LinkedList :"+add1(ll));
        System.out.println();
        System.out.println("= 중간에 추가하기 =");
        System.out.println("ArrayList :"+add2(al));
        System.out.println("LinkedList :"+add2(ll));
        System.out.println();
        System.out.println("= 중간에서 삭제하기 =");
        System.out.println("ArrayList :"+remove2(al));
        System.out.println("LinkedList :"+remove2(ll));
        System.out.println();
        System.out.println("= 순차적으로 삭제하기 =");
        System.out.println("ArrayList :"+remove1(al));
        System.out.println("LinkedList :"+remove1(ll));
    }

    public static long add1(List list) {
        long start = System.currentTimeMillis();
        for(int i=0; i<1000000;i++) list.add(i+"");
        long end = System.currentTimeMillis();
        return end - start;
    }

    public static long add2(List list) {
        long start = System.currentTimeMillis();
        for(int i=0; i<10000;i++) list.add(500, "X");
        long end = System.currentTimeMillis();
        return end - start;
    }

    public static long remove1(List list) {
        long start = System.currentTimeMillis();
        for(int i=list.size()-1; i >= 0;i--) list.remove(i);
        long end = System.currentTimeMillis();
        return end - start;
    }

    public static long remove2(List list) {
        long start = System.currentTimeMillis();
        for(int i=0; i<10000;i++) list.remove(i);
        long end = System.currentTimeMillis();
        return end - start;
    }
}
```
```
= 순차적으로 추가하기 =
ArrayList :117
LinkedList :118

= 중간에 추가하기 =
ArrayList :1137
LinkedList :13

= 중간에서 삭제하기 =
ArrayList :1030
LinkedList :136

= 순차적으로 삭제하기 =
ArrayList :7
LinkedList :16

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-6
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String args[]) {
        ArrayList  al = new ArrayList(1000000);
        LinkedList ll = new LinkedList();
        add(al);
        add(ll);

        System.out.println("= 접근시간테스트 =");
        System.out.println("ArrayList :"+access(al));
        System.out.println("LinkedList :"+access(ll));
    }

    public static void add(List list) {
        for(int i=0; i<100000;i++) list.add(i+"");
    }

    public static long access(List list) {
        long start = System.currentTimeMillis();

        for(int i=0; i<10000;i++) list.get(i);

        long end = System.currentTimeMillis();

        return end - start;
    }
}
```
```
= 접근시간테스트 =
ArrayList :1
LinkedList :214

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 스택과 큐에 대해 알아보겠습니다. 스택이란 마지막에 저장한 데이터를 가장 먼저 꺼내게 되는 LIFO(Last In First Out)구조로 되어 있고, 큐는 처음에 저장한 데이터를 가장 먼저 꺼내게 되는 FIFO(First In First Out)구조로 되어 있습니다. 쉽게 얘기하면 스택은 동전통과 같은 구조로 양 옆과 바닥이 막혀 있어서 한 방향으로만 뺄 수 있는 구조이고, 큐는 양 옆만 막혀 있고 위아래로 뚫려 있어서 한 방향으로는 넣고 다른 한 방향으로는 빼는 파이프와 같은 구조로 되어 있습니다. 이러한 스택과 큐와 관련된 예제는 다음과 같이 6개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-7
```java
import java.util.*;

class StackQueueEx {
    public static void main(String[] args) {
        Stack st = new Stack();
        Queue q = new LinkedList();	 // Queue인터페이스의 구현체인 LinkedList를 사용

        st.push("0");
        st.push("1");
        st.push("2");

        q.offer("0");
        q.offer("1");
        q.offer("2");

        System.out.println("= Stack =");
        while(!st.empty()) {
            System.out.println(st.pop());
        }

        System.out.println("= Queue =");
        while(!q.isEmpty()) {
            System.out.println(q.poll());
        }
    }
}
```
```
= Stack =
2
1
0
= Queue =
0
1
2

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-8
```java
import java.util.*;

class MyStack extends Vector {
    public Object push(Object item) {
        addElement(item);
        return item;
    }

    public Object pop() {
        Object obj = peek();	 // Stack에 저장된 마지막 요소를 읽어온다,
        //   만일 Stack이 비어있으면 peek()메서드가 EmptyStackException을 발생시킨다.
        //   마지막 요소를 삭제한다. 배열의 index가 0 부터 시작하므로 1을 빼준다.
        removeElementAt(size() - 1);

        return obj;
    }

    public Object peek() {
        int	len = size();

        if (len == 0)
            throw new EmptyStackException();
        // 마지막 요소를 반환한다. 배열의 index가 0부터 시작하므로 1을 빼준다.
        return elementAt(len - 1);
    }

    public boolean empty() {
        return size() == 0;
    }

    public int search(Object o) {
        int i = lastIndexOf(o);	// 끝에서부터 객체를 찾는다.
        // 반환값은 저장된 위치(배열의 index)dlek.
        if (i >= 0) { // ��ü�� ã�� ���
            return size() - i; // Stack은 맨 위에 저장된 객체의 index를 1로 정의하기 때문에
            // 계산을 통해서 구한다.
        }

        return - 1;		// 해당 객체를 찾지 못하면 -1을 반환한다.
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-9
```java
import java.util.*;

public class HelloWorldPrint {
    public static Stack back    = new Stack();
    public static Stack forward = new Stack();

    public static void main(String[] args) {
        goURL("1.네이트");
        goURL("2.야후");
        goURL("3.네이버");
        goURL("4.다음");

        printStatus();

        goBack();
        System.out.println("= '뒤로' 버튼을 누른 후 =");
        printStatus();

        goBack();
        System.out.println("= '뒤로' 버튼을 누른 후 =");
        printStatus();

        goForward();
        System.out.println("= '앞으로' 버튼을 누른 후 =");
        printStatus();

        goURL("codechobo.com");
        System.out.println("= 새로운 주소로 이동 후 =");
        printStatus();
    }

    public static void printStatus() {
        System.out.println("back:"+back);
        System.out.println("forward:"+forward);
        System.out.println("현재화면은 '" + back.peek()+"' 입니다.");
        System.out.println();
    }

    public static void goURL(String url){
        back.push(url);
        if(!forward.empty())
            forward.clear();
    }

    public static void goForward(){
        if(!forward.empty())
            back.push(forward.pop());
    }

    public static void goBack(){
        if(!back.empty())
            forward.push(back.pop());
    }
}
```
```
back:[1.네이트, 2.야후, 3.네이버, 4.다음]
forward:[]
현재화면은 '4.다음' 입니다.

= '뒤로' 버튼을 누른 후 =
back:[1.네이트, 2.야후, 3.네이버]
forward:[4.다음]
현재화면은 '3.네이버' 입니다.

= '뒤로' 버튼을 누른 후 =
back:[1.네이트, 2.야후]
forward:[4.다음, 3.네이버]
현재화면은 '2.야후' 입니다.

= '앞으로' 버튼을 누른 후 =
back:[1.네이트, 2.야후, 3.네이버]
forward:[4.다음]
현재화면은 '3.네이버' 입니다.

= 새로운 주소로 이동 후 =
back:[1.네이트, 2.야후, 3.네이버, codechobo.com]
forward:[]
현재화면은 'codechobo.com' 입니다.


Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-10
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String[] args) {
        if(args.length!=1){
            System.out.println("Usage : java ExpValidCheck \"EXPRESSION\"");
            System.out.println("Example : java ExpValidCheck \"((2+3)*1)+3\"");
            System.exit(0);
        }

        Stack st = new Stack();
        String expression = args[0];

        System.out.println("expression:"+expression);

        try {
            for(int i=0; i < expression.length();i++){
                char ch = expression.charAt(i);

                if(ch=='('){
                    st.push(ch+"");
                } else if(ch==')') {
                    st.pop();
                }
            }

            if(st.isEmpty()){
                System.out.println("괄호가 일치합니다.");
            } else {
                System.out.println("괄호가 일치하지 않습니다.");
            }
        } catch (EmptyStackException e) {
            System.out.println("괄호가 일치하지 않습니다.");
        } // try
    }
}
```
```
Usage : java ExpValidCheck "EXPRESSION"
Example : java ExpValidCheck "((2+3)*1)+3"

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예제 11-10에서 문서와 다르게 다음 실행문이 출력되지 않아서 이 부분은 확인 후 수정하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-11
```java
import java.util.*;

class QueueEx1 {
    static Queue q = new LinkedList();
    static final int MAX_SIZE = 5;	// Queue에 최대 5개까지만 저장되도록 한다.

    public static void main(String[] args) {
        System.out.println("help를 입력하면 도움말을 볼 수 있습니다.");

        while(true) {
            System.out.print(">>");
            try {
                // 화면으로부터 라인단위로 입력받는다.
                Scanner s = new Scanner(System.in);
                String input = s.nextLine().trim();

                if("".equals(input)) continue;

                if(input.equalsIgnoreCase("q")) {
                    System.exit(0);
                } else if(input.equalsIgnoreCase("help")) {
                    System.out.println(" help - 도움말을 보여줍니다.");
                    System.out.println(" q 또는 Q - 프로그램을 종료합니다.");
                    System.out.println(" history - 최근에 입력한 명령어를 " + MAX_SIZE +"개 보여줍니다.");
                } else if(input.equalsIgnoreCase("history")) {
                    int i=0;
                    // 입력받은 명령어를 저장하고,
                    save(input);

                    // LinkedList의 내용을 보여준다.
                    LinkedList tmp = (LinkedList)q;
                    ListIterator it = tmp.listIterator();

                    while(it.hasNext()) {
                        System.out.println(++i+"."+it.next());
                    }
                } else {
                    save(input);
                    System.out.println(input);
                } // if(input.equalsIgnoreCase("q")) {
            } catch(Exception e) {
                System.out.println("입력오류입니다.");
            }
        } // while(true)
    } //  main()

    public static void save(String input) {
        // queue에 저장한다.
        if(!"".equals(input))
            q.offer(input);

        // queue의 최대크기를 넘으면 제일 처음 입력된 것을 삭제한다.,
        if(q.size() > MAX_SIZE)  // size()는 Collection인터페이스에 정의
            q.remove();
    }
} // end of class
```
```
help를 입력하면 도움말을 볼 수 있습니다.
>>help
 help - 도움말을 보여줍니다.
 q 또는 Q - 프로그램을 종료합니다.
 history - 최근에 입력한 명령어를 5개 보여줍니다.
>>dir
dir
>>cd
cd
>>mkdir
mkdir
>>dir
dir
>>history
1.dir
2.cd
3.mkdir
4.dir
5.history
>>q

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-12
```java
import java.util.*;

class PriorityQueueEx {
    public static void main(String[] args) {
        Queue pq = new PriorityQueue();
        pq.offer(3);  // pq.offer(new Integer(3)); 오토박싱
        pq.offer(1);
        pq.offer(5);
        pq.offer(2);
        pq.offer(4);

        System.out.println(pq); // pq의 내부 배열을 출력

        Object obj = null;

        // PriorityQueue에 저장된 요소를 하나씩 꺼낸다.
        while((obj = pq.poll())!=null)
            System.out.println(obj);
    }
}
```
```
[1, 2, 5, 3, 4]
1
2
3
4
5

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
Iterator, ListIterator, Enumeration과 관련된 예제는 다음과 같이 5개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-13
```java
import java.util.*;

class IteratorEx1 {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add("1");
        list.add("2");
        list.add("3");
        list.add("4");
        list.add("5");

        Iterator it = list.iterator();
        while(it.hasNext()) {
            Object obj = it.next();
            System.out.println(obj);
        }
    } // main
}
```
```
1
2
3
4
5

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-14
```java
import java.util.*;

class ListIteratorEx1 {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add("1");
        list.add("2");
        list.add("3");
        list.add("4");
        list.add("5");

        ListIterator it = list.listIterator();

        while(it.hasNext()) {
            System.out.print(it.next()); // 순방향으로 진행하면서 읽어온다.
        }
        System.out.println();

        while(it.hasPrevious()) {
            System.out.print(it.previous()); // 역방향으로 진행하면서 읽어온다.
        }
        System.out.println();
    }
}
```
```
12345
54321

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-15
```java
import java.util.*;

public class HelloWorldPrint {
    public static void main(String[] args) {
        ArrayList original = new ArrayList(10);
        ArrayList copy1    = new ArrayList(10);
        ArrayList copy2    = new ArrayList(10);

        for(int i=0; i < 10; i++)
            original.add(i+"");

        Iterator it = original.iterator();

        while(it.hasNext())
            copy1.add(it.next());

        System.out.println("= Original에서 copy1로 복사 (copy) =");
        System.out.println("original:"+original);
        System.out.println("copy1:"+copy1);
        System.out.println();

        it = original.iterator(); // Iterator는 재사용이 안되므로, 다시 얻어와야 한다.

        while(it.hasNext()){
            copy2.add(it.next());
            it.remove();
        }

        System.out.println("= Original에서 copy2로 이동(move) =");
        System.out.println("original:"+original);
        System.out.println("copy2:"+copy2);
    } // main
} // class
```
```
= Original에서 copy1로 복사 (copy) =
original:[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
copy1:[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

= Original에서 copy2로 이동(move) =
original:[]
copy2:[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-16
```java
import java.util.*;

public class MyVector2 extends MyVector implements Iterator {
    int cursor  = 0;
    int lastRet = -1;

    public MyVector2(int capacity) {
        super(capacity);
    }

    public MyVector2() {
        this(10);
    }

    public String toString() {
        String tmp = "";
        Iterator it = iterator();

        for(int i=0; it.hasNext();i++) {
            if(i!=0) tmp+=", ";
            tmp += it.next(); 	// tmp += next().toString();
        }

        return "["+ tmp +"]";
    }

    public Iterator iterator() {
        cursor=0;		// cursor와 lastRet를 초기화한다.
        lastRet = -1;
        return this;
    }

    public boolean hasNext() {
        return cursor != size();
    }

    public Object next(){
        Object next = get(cursor);
        lastRet = cursor++;
        return next;
    }

    public void remove() {
        // 더이상 삭제할 것이 없으면 IllegalStateException를 발생시킨다.
        if(lastRet==-1) {
            throw new IllegalStateException();
        } else {
            remove(lastRet);
            cursor--;           // 삭제 후에 cursor의 위치를 감소시킨다.
            lastRet = -1;		// lastRet의 값을 초기화한다.
        }
    }
} // class
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-17
```java
import java.util.*;

class MyVector2Test {
    public static void main(String args[]) {
        MyVector2 v = new MyVector2();
        v.add("0");
        v.add("1");
        v.add("2");
        v.add("3");
        v.add("4");

        System.out.println("삭제 전 : " + v);
        Iterator it = v.iterator();
        it.next();
        it.remove();
        it.next();
        it.remove();

        System.out.println("삭제 전 : " + v);
    }
}
```
```
삭제 전 : [0, 1, 2, 3, 4]
삭제 전 : [2, 3, 4]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예제 11-17은 11-16와 11-4가 있어야 작동되는 코드인 점 참고하시기 바랍니다.
________________________________________________________________________________________________________________________________________________________________________
Arrays클래스와 관련된 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-18
```java
import java.util.*;

class ArraysEx {
    public static void main(String[] args) {
        int[]	arr   =  {0,1,2,3,4};
        int[][] arr2D =  {{11,12,13}, {21,22,23}};

        System.out.println("arr="+Arrays.toString(arr));
        System.out.println("arr2D="+Arrays.deepToString(arr2D));

        int[] arr2 = Arrays.copyOf(arr, arr.length);
        int[] arr3 = Arrays.copyOf(arr, 3);
        int[] arr4 = Arrays.copyOf(arr, 7);
        int[] arr5 = Arrays.copyOfRange(arr, 2, 4);
        int[] arr6 = Arrays.copyOfRange(arr, 0, 7);

        System.out.println("arr2="+ Arrays.toString(arr2));
        System.out.println("arr3="+ Arrays.toString(arr3));
        System.out.println("arr4="+ Arrays.toString(arr4));
        System.out.println("arr5="+ Arrays.toString(arr5));
        System.out.println("arr6="+ Arrays.toString(arr6));

        int[] arr7 =  new int[5];
        Arrays.fill(arr7, 9);  // arr=[9,9,9,9,9]
        System.out.println("arr7="+Arrays.toString(arr7));

        Arrays.setAll(arr7, i -> (int)(Math.random()*6)+1);
        System.out.println("arr7="+Arrays.toString(arr7));

        for(int i : arr7) {
            char[] graph = new char[i];
            Arrays.fill(graph, '*');
            System.out.println(new String(graph)+i);
        }

        String[][] str2D  = new String[][]{{"aaa","bbb"},{"AAA","BBB"}};
        String[][] str2D2 = new String[][]{{"aaa","bbb"},{"AAA","BBB"}};

        System.out.println(Arrays.equals(str2D, str2D2));      // false
        System.out.println(Arrays.deepEquals(str2D, str2D2));  // true

        char[] chArr = { 'A', 'D', 'C',  'B', 'E' };

        int idx = Arrays.binarySearch(chArr, 'B');
        System.out.println("chArr="+Arrays.toString(chArr));
        System.out.println("index of B ="+Arrays.binarySearch(chArr, 'B'));
        System.out.println("= After sorting =");
        Arrays.sort(chArr);
        System.out.println("chArr="+Arrays.toString(chArr));
        System.out.println("index of B ="+Arrays.binarySearch(chArr, 'B'));
    }
}
```
```
arr=[0, 1, 2, 3, 4]
arr2D=[[11, 12, 13], [21, 22, 23]]
arr2=[0, 1, 2, 3, 4]
arr3=[0, 1, 2]
arr4=[0, 1, 2, 3, 4, 0, 0]
arr5=[2, 3]
arr6=[0, 1, 2, 3, 4, 0, 0]
arr7=[9, 9, 9, 9, 9]
arr7=[4, 2, 2, 1, 1]
****4
**2
**2
*1
*1
false
true
chArr=[A, D, C, B, E]
index of B =-2
= After sorting =
chArr=[A, B, C, D, E]
index of B =1

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
Comparator 및 Comparable클래스와 관련된 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-19
```java
import java.util.*;

class ComparatorEx {
    public static void main(String[] args) {
        String[] strArr = {"cat", "Dog", "lion", "tiger"};

        Arrays.sort(strArr); // String의 Comparable구현에 의한 정렬
        System.out.println("strArr=" + Arrays.toString(strArr));

        Arrays.sort(strArr, String.CASE_INSENSITIVE_ORDER); // 대소문자 구분안함
        System.out.println("strArr=" + Arrays.toString(strArr));

        Arrays.sort(strArr, new Descending()); // 역순 정렬
        System.out.println("strArr=" + Arrays.toString(strArr));
    }
}

class Descending implements Comparator {
    public int compare(Object o1, Object o2){
        if( o1 instanceof Comparable && o2 instanceof Comparable) {
            Comparable c1 = (Comparable)o1;
            Comparable c2 = (Comparable)o2;
            return c1.compareTo(c2) * -1 ; // -1을 곱해서 기본 정렬방식의 역으로 변경한다.
            // 또는 c2.compareTo(c1)와 같이 순서를 바꿔도 된다.

        }
        return -1;
    }
} 
```
```
strArr=[Dog, cat, lion, tiger]
strArr=[cat, Dog, lion, tiger]
strArr=[tiger, lion, cat, Dog]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
HashSet에 대한 예제는 다음과 같이 6개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-20
```java
import java.util.*;

class HashSetEx1 {
    public static void main(String[] args) {
        Object[] objArr = {"1",new Integer(1),"2","2","3","3","4","4","4"};
        Set set = new HashSet();

        for(int i=0; i < objArr.length; i++) {
            set.add(objArr[i]);	// HashSet에 objArr의 요소들을 저장한다.
        }
        // HashSet에 저장된 요소들을 출력한다.
        System.out.println(set);
    }
}
```
```
[1, 1, 2, 3, 4]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-21
```java
import java.util.*;

class HashSetLotto {
    public static void main(String[] args) {
        Set set = new HashSet();

        for (int i = 0; set.size() < 6 ; i++) {
            int num = (int)(Math.random()*45) + 1;
            set.add(new Integer(num));
        }

        List list = new LinkedList(set);	// LinkedList(Collection c)
        Collections.sort(list);				// Collections.sort(List list)
        System.out.println(list);
    }
}
```
```
[10, 21, 28, 30, 38, 39]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-22
```java
import java.util.*;

class Bingo {
    public static void main(String[] args) {
        Set set = new HashSet();
//          Set set = new LinkedHashSet();
        int[][] board = new int[5][5];

        for(int i=0; set.size() < 25; i++) {
            set.add((int)(Math.random()*50)+1+"");
        }

        Iterator it = set.iterator();

        for(int i=0; i < board.length; i++) {
            for(int j=0; j < board[i].length; j++) {
                board[i][j] = Integer.parseInt((String)it.next());
                System.out.print((board[i][j] < 10 ? "  " : " ") + board[i][j]);
            }
            System.out.println();
        }
    } // main
} 
```
```
 44 46 47 26 49
 28 29 50 30 10
 11 34 36 37 18
  1  4  6  7  9
 40 41 20 42 43

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-23
```java
import java.util.*;

class HashSetEx3 {
    public static void main(String[] args) {
        HashSet set = new HashSet();

        set.add("abc");
        set.add("abc");
        set.add(new Person("David",10));
        set.add(new Person("David",10));

        System.out.println(set);
    }
}

class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String toString() {
        return name +":"+ age;
    }
}
```
```
[abc, David:10, David:10]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-24
```java
import java.util.*;

class HashSetEx4 {
    public static void main(String[] args) {
        HashSet set = new HashSet();

        set.add(new String("abc"));
        set.add(new String("abc"));
        set.add(new Person2("David",10));
        set.add(new Person2("David",10));

        System.out.println(set);
    }
}

class Person2 {
    String name;
    int age;

    Person2(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public boolean equals(Object obj) {
        if(obj instanceof Person2) {
            Person2 tmp = (Person2)obj;
            return name.equals(tmp.name) && age==tmp.age;
        }

        return false;
    }

    public int hashCode() {
        return (name+age).hashCode();
    }

    public String toString() {
        return name +":"+ age;
    }
}
```
```
[abc, David:10]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-25
```java
import java.util.*;

class HashSetEx5 {
    public static void main(String args[]) {
        HashSet setA    = new HashSet();
        HashSet setB    = new HashSet();
        HashSet setHab = new HashSet();
        HashSet setKyo = new HashSet();
        HashSet setCha = new HashSet();

        setA.add("1");		setA.add("2");
        setA.add("3");		setA.add("4");
        setA.add("5");
        System.out.println("A = "+setA);

        setB.add("4");		setB.add("5");
        setB.add("6");		setB.add("7");
        setB.add("8");
        System.out.println("B = "+setB);

        Iterator it = setB.iterator();
        while(it.hasNext()) {
            Object tmp = it.next();
            if(setA.contains(tmp))
                setKyo.add(tmp);
        }

        it = setA.iterator();
        while(it.hasNext()) {
            Object tmp = it.next();
            if(!setB.contains(tmp))
                setCha.add(tmp);
        }

        it = setA.iterator();
        while(it.hasNext())
            setHab.add(it.next());

        it = setB.iterator();
        while(it.hasNext())
            setHab.add(it.next());

        System.out.println("A ∩ B = "+setKyo);  // 한글 ㄷ을 누르고 한자키
        System.out.println("A ∪ B = "+setHab);  // 한글 ㄷ을 누르고 한자키
        System.out.println("A - B = "+setCha);
    }
}
```
```
A = [1, 2, 3, 4, 5]
B = [4, 5, 6, 7, 8]
A ∩ B = [4, 5]
A ∪ B = [1, 2, 3, 4, 5, 6, 7, 8]
A - B = [1, 2, 3]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
TreeSet과 관련된 예제는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-26
```java
import java.util.*;

class TreeSetLotto {
    public static void main(String[] args) {
        Set set = new TreeSet();

        for (int i = 0; set.size() < 6 ; i++) {
            int num = (int)(Math.random()*45) + 1;
            set.add(num);  // set.add(new Integer(num));
        }

        System.out.println(set);
    }
}
```
```
[1, 4, 17, 18, 21, 37]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-27
```java
import java.util.*;

class TreeSetEx1 {
    public static void main(String[] args) {
        TreeSet set = new TreeSet();

        String from = "b";
        String to	= "d";

        set.add("abc");
        set.add("alien");
        set.add("bat");
        set.add("car");
        set.add("Car");
        set.add("disc");
        set.add("dance");
        set.add("dZZZZ");
        set.add("dzzzz");
        set.add("elephant");
        set.add("elevator");
        set.add("fan");
        set.add("flower");

        System.out.println(set);
        System.out.println("range search : from " + from  +" to "+ to);
        System.out.println("result1 : " + set.subSet(from, to));
        System.out.println("result2 : " + set.subSet(from, to + "zzz"));
    }
}
```
```
[Car, abc, alien, bat, car, dZZZZ, dance, disc, dzzzz, elephant, elevator, fan, flower]
range search : from b to d
result1 : [bat, car]
result2 : [bat, car, dZZZZ, dance, disc]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-28
```java
class AsciiPrint{
    public static void main(String[] args) {
        char ch = ' ';
        for(int i=0; i < 95; i++)  // 공백(' ')이후의 문자들을 출력한다.
            System.out.print(ch++);
    }
}
```
```
 !"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-29
```java
import java.util.*;

class TreeSetEx2 {
    public static void main(String[] args) {
        TreeSet set = new TreeSet();
        int[] score = {80, 95, 50, 35, 45, 65, 10, 100};

        for(int i=0; i < score.length; i++)
            set.add(new Integer(score[i]));

        System.out.println("50보다 작은 값 :"	+ set.headSet(new Integer(50)));
        System.out.println("50보다 큰 값 :"	+ set.tailSet(new Integer(50)));
    }
}
```
```
50보다 작은 값 :[10, 35, 45]
50보다 큰 값 :[50, 65, 80, 95, 100]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
HashMap과 Hashtable에 대한 예제는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-30
```java
import java.util.*;

class HashMapEx1 {
    public static void main(String[] args) {
        HashMap map = new HashMap();
        map.put("myId", "1234");
        map.put("asdf", "1111");
        map.put("asdf", "1234");

        Scanner s = new Scanner(System.in);	// 화면으로부터 라인단위로 입력받는다.

        while(true) {
            System.out.println("id와 password를 입력해주세요.");
            System.out.print("id :");
            String id = s.nextLine().trim();

            System.out.print("password :");
            String password = s.nextLine().trim();
            System.out.println();

            if(!map.containsKey(id)) {
                System.out.println("입력하신 id는 존재하지 않습니다. 다시 입력해주세요.");
                continue;
            } else {
                if(!(map.get(id)).equals(password)) {
                    System.out.println("비밀번호가 일치하지 않습니다. 다시 입력해주세요.");
                } else {
                    System.out.println("id와 비밀번호가 일치합니다.");
                    break;
                }
            }
        } // while
    } // main의 끝
}
```
```
id와 password를 입력해주세요.
id :asdf
password :1111

비밀번호가 일치하지 않습니다. 다시 입력해주세요.
id와 password를 입력해주세요.
id :asdf
password :1234

id와 비밀번호가 일치합니다.

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-31
```java
import java.util.*;

class HashMapEx2 {
    public static void main(String[] args) {
        HashMap map = new HashMap();
        map.put("김자바", new Integer(90));
        map.put("이자바", new Integer(100));
        map.put("강자바", new Integer(100));
        map.put("안자바", new Integer(80));
        map.put("정자바", new Integer(90));

        Set set = map.entrySet();
        Iterator it = set.iterator();

        while(it.hasNext()) {
            Map.Entry e = (Map.Entry)it.next();
            System.out.println("이름 : "+ e.getKey() + ", 점수 : " + e.getValue());
        }

        set = map.keySet();
        System.out.println("참가자 명단 : " + set);

        Collection values = map.values();
        it = values.iterator();

        int total = 0;

        while(it.hasNext()) {
            Integer i = (Integer)it.next();
            total += i.intValue();
        }

        System.out.println("총점 : " + total);
        System.out.println("평균 : " + (float)total/set.size());
        System.out.println("최고점수 : " + Collections.max(values));
        System.out.println("최저점수 : " + Collections.min(values));
    }
}
```
```
이름 : 안자바, 점수 : 80
이름 : 김자바, 점수 : 90
이름 : 강자바, 점수 : 100
이름 : 이자바, 점수 : 100
이름 : 정자바, 점수 : 90
참가자 명단 : [안자바, 김자바, 강자바, 이자바, 정자바]
총점 : 460
평균 : 92.0
최고점수 : 100
최저점수 : 80

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-32
```java
import java.util.*;

class HashMapEx3 {
    static HashMap phoneBook = new HashMap();

    public static void main(String[] args) {
        addPhoneNo("친구", "이자바", "010-111-1111");
        addPhoneNo("친구", "김자바", "010-222-2222");
        addPhoneNo("친구", "김자바", "010-333-3333");
        addPhoneNo("회사", "김대리", "010-444-4444");
        addPhoneNo("회사", "김대리", "010-555-5555");
        addPhoneNo("회사", "박대리", "010-666-6666");
        addPhoneNo("회사", "이과장", "010-777-7777");
        addPhoneNo("세탁", "010-888-8888");

        printList();
    } // main


    static void addGroup(String groupName) {
        if(!phoneBook.containsKey(groupName))
            phoneBook.put(groupName, new HashMap());
    }

    // 그룹에 전화번호를 추가하는 메서드
    static void addPhoneNo(String groupName, String name, String tel) {
        addGroup(groupName);
        HashMap group = (HashMap)phoneBook.get(groupName);
        group.put(tel, name);	// 이름은 중복될 수 있으니 전화번호를 key로 저장한다.
    }

    static void addPhoneNo(String name, String tel) {
        addPhoneNo("기타", name, tel);
    }

    // 전화번호부 전체를 출력하는 메서드
    static void printList() {
        Set set = phoneBook.entrySet();
        Iterator it = set.iterator();

        while(it.hasNext()) {
            Map.Entry e = (Map.Entry)it.next();

            Set subSet = ((HashMap)e.getValue()).entrySet();
            Iterator subIt = subSet.iterator();

            System.out.println(" * "+e.getKey()+"["+subSet.size()+"]");

            while(subIt.hasNext()) {
                Map.Entry subE = (Map.Entry)subIt.next();
                String telNo = (String)subE.getKey();
                String name = (String)subE.getValue();
                System.out.println(name + " " + telNo );
            }
            System.out.println();
        }
    } // printList()
} // class
```
```
 * 기타[1]
세탁 010-888-8888

 * 친구[3]
이자바 010-111-1111
김자바 010-222-2222
김자바 010-333-3333

 * 회사[4]
이과장 010-777-7777
김대리 010-444-4444
김대리 010-555-5555
박대리 010-666-6666


Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-33
```java
import java.util.*;

class HashMapEx4 {
    public static void main(String[] args) {
        String[] data = { "A","K","A","K","D","K","A","K","K","K","Z","D" };

        HashMap map = new HashMap();

        for(int i=0; i < data.length; i++) {
            if(map.containsKey(data[i])) {
                Integer value = (Integer)map.get(data[i]);
                map.put(data[i], new Integer(value.intValue() + 1));
            } else {
                map.put(data[i], new Integer(1));
            }
        }

        Iterator it = map.entrySet().iterator();

        while(it.hasNext()) {
            Map.Entry entry = (Map.Entry)it.next();
            int value = ((Integer)entry.getValue()).intValue();
            System.out.println(entry.getKey() + " : " + printBar('#', value) + " " + value );
        }
    } // main

    public static String printBar(char ch, int value) {
        char[] bar = new char[value];

        for(int i=0; i < bar.length; i++) {
            bar[i] = ch;
        }

        return new String(bar); 	// String(char[] chArr)
    }
}
```
```
A : ### 3
D : ## 2
Z : # 1
K : ###### 6

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
TreeMap과 관련된 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-34
```java
import java.util.*;

class TreeMapEx1 {
    public static void main(String[] args) {
        String[] data = { "A","K","A","K","D","K","A","K","K","K","Z","D" };

        TreeMap map = new TreeMap();

        for(int i=0; i < data.length; i++) {
            if(map.containsKey(data[i])) {
                Integer value = (Integer)map.get(data[i]);
                map.put(data[i], new Integer(value.intValue() + 1));
            } else {
                map.put(data[i], new Integer(1));
            }
        }

        Iterator it = map.entrySet().iterator();

        System.out.println("= 기본정렬 =");
        while(it.hasNext()) {
            Map.Entry entry = (Map.Entry)it.next();
            int value = ((Integer)entry.getValue()).intValue();
            System.out.println(entry.getKey() + " : " + printBar('#', value) + " " + value );
        }
        System.out.println();

        // map을 ArrayList로 변환한 다음에 Collections.sort()로 정렬
        Set set = map.entrySet();
        List list = new ArrayList(set);	// ArrayList(Collection c)

        // static void sort(List list, Comparator c)
        Collections.sort(list, new ValueComparator());

        it = list.iterator();

        System.out.println("= 값의 크기가 큰 순서로 정렬 =");
        while(it.hasNext()) {
            Map.Entry entry = (Map.Entry)it.next();
            int value = ((Integer)entry.getValue()).intValue();
            System.out.println(entry.getKey() + " : " + printBar('#', value) + " " + value );
        }

    } // 	public static void main(String[] args)

    static class ValueComparator implements Comparator {
        public int compare(Object o1, Object o2) {
            if(o1 instanceof Map.Entry && o2 instanceof Map.Entry) {
                Map.Entry e1 = (Map.Entry)o1;
                Map.Entry e2 = (Map.Entry)o2;

                int v1 = ((Integer)e1.getValue()).intValue();
                int v2 = ((Integer)e2.getValue()).intValue();

                return  v2 - v1;
            }
            return -1;
        }
    }	// 	static class ValueComparator implements Comparator {

    public static String printBar(char ch, int value) {
        char[] bar = new char[value];

        for(int i=0; i < bar.length; i++) {
            bar[i] = ch;
        }

        return new String(bar);
    }
}
```
```
= 기본정렬 =
A : ### 3
D : ## 2
K : ###### 6
Z : # 1

= 값의 크기가 큰 순서로 정렬 =
K : ###### 6
A : ### 3
D : ## 2
Z : # 1

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
Properties와 관련된 예제는 다음과 같이 4개가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-35
```java
import java.util.*;

class PropertiesEx1 {
    public static void main(String[] args) {
        Properties prop = new Properties();

        // prop에 키와 값(key, value)을 저장한다.
        prop.setProperty("timeout","30");
        prop.setProperty("language","kr");
        prop.setProperty("size","10");
        prop.setProperty("capacity","10");

        // prop에 저장된 요소들을 Enumeration을 이용해서 출력한다.
        Enumeration e = prop.propertyNames();

        while(e.hasMoreElements()) {
            String element = (String)e.nextElement();
            System.out.println(element + "="+ prop.getProperty(element));
        }

        System.out.println();
        prop.setProperty("size","20");	// size의 값을 20으로 변경한다.
        System.out.println("size="       + prop.getProperty("size"));
        System.out.println("capacity="   + prop.getProperty("capacity", "20"));
        System.out.println("loadfactor=" + prop.getProperty("loadfactor", "0.75"));

        System.out.println(prop);	// prop에 저장된 요소들을 출력한다.
        prop.list(System.out);      // prop에 저장된 요소들을 화면(System.out)에 출력한다.
    }
}
```
```
capacity=10
size=10
timeout=30
language=kr

size=20
capacity=10
loadfactor=0.75
{capacity=10, size=20, timeout=30, language=kr}
-- listing properties --
capacity=10
size=20
timeout=30
language=kr

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
예제 11-36, 11-37은 외부파일로부터 데이터를 입력받아서 계산결과를 보여주어야 하므로 다음 예제로 넘어가겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-38
```java
import java.util.*;

class PropertiesEx4{
    public static void main(String[] args) {
        Properties sysProp = System.getProperties();
        System.out.println("java.version :" + sysProp.getProperty("java.version"));
        System.out.println("user.languag :" + sysProp.getProperty("user.language"));
        sysProp.list(System.out);
    }
}
```
```
java.version :1.8.0_333
user.languag :ko
-- listing properties --
java.runtime.name=Java(TM) SE Runtime Environment
sun.boot.library.path=/Library/Java/JavaVirtualMachines/jdk...
java.vm.version=25.333-b02
...
socksNonProxyHosts=local|*.local|169.254/16|*.169.254/16...
ftp.nonProxyHosts=local|*.local|169.254/16|*.169.254/16...
sun.cpu.isalist=

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
마지막으로, Collections와 관련된 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- ● 예제 11-39
```java
import java.util.*;
import static java.util.Collections.*;

public class HelloWorldPrint{
    public static void main(String[] args) {
        List list = new ArrayList();
        System.out.println(list);

        addAll(list, 1,2,3,4,5);
        System.out.println(list);

        // 오른쪽으로 두 칸씩 이동
        rotate(list, 2);
        System.out.println(list);

        // 첫번째와 세번째를 교환
        swap(list, 0, 2);
        System.out.println(list);

        // 저장된 요소의 위치를 임의로 변경
        shuffle(list);
        System.out.println(list);

        // 정렬
        sort(list);
        System.out.println(list);

        // 오름차순 정렬
        sort(list, reverseOrder());
        System.out.println(list);

        System.out.println("max = " + max(list));
        System.out.println("min = " + min(list));
        System.out.println("min = " + max(list, reverseOrder()));

        // list를 9로 채움
        fill(list, 9);
        System.out.println("list = " + list);

        // list와 같은 크기의 새로운 list를 생성하고 2로 채움
        List newList = nCopies(list.size(), 2);
        System.out.println("newList = " + newList);

        // 공통요소가 없으면 true
        System.out.println(disjoint(list, newList));

        copy(list, newList);
        System.out.println("newList = " + newList);
        System.out.println("list = " + list);

        replaceAll(list, 2, 1);
        System.out.println("list = " + list);

        Enumeration enumeration = enumeration(list);
        ArrayList list2 = list(enumeration);
        System.out.println("list2 = " + list2);
    }
}
```
```
[]
[1, 2, 3, 4, 5]
[4, 5, 1, 2, 3]
[1, 5, 4, 2, 3]
[2, 4, 5, 1, 3]
[1, 2, 3, 4, 5]
[5, 4, 3, 2, 1]
max = 5
min = 1
min = 1
list = [9, 9, 9, 9, 9]
newList = [2, 2, 2, 2, 2]
true
newList = [2, 2, 2, 2, 2]
list = [2, 2, 2, 2, 2]
list = [1, 1, 1, 1, 1]
list2 = [1, 1, 1, 1, 1]

Process finished with exit code 0
```
________________________________________________________________________________________________________________________________________________________________________
여기까지 컬렉션 프레임워크에 대해 알아보았습니다.다음 포스팅에서는 지네릭스, 열거형, 애너테이션에 대해 확인해보겠습니다. 오늘도 긴 글 읽어주시느라 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 자바의 정석(3판) : http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788994492032&orderClick=LAG&Kc=