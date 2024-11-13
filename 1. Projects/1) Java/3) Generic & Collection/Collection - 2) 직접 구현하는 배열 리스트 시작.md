---
tags:
  - java
  - collection
  - arrayList
  - 자료구조
---

# List 자료 구조
>[!Bug] 배열의 불편함
>1. 배열의 길이를 동적으로 변경할 수 없다.
>2. 데이터를 추가하기 불편하다.
>	- 데이터를 추가하는 경우 직접 오른쪽으로 한 칸씩 데이터를 밀어야 한다.
>	- 이런 코드를 직접 작성해야 한다.


>[!info] List 자료구조
>**순서**가 있고, **중복을 허용**하는 자료구조를 **리스트**라고 한다.
>- 배열: 순서가 있고, 중복을 허용하지만 **크기가 정적으로 고정됨**
>- 리스트: 순서가 있고, 중복을 허용하지만 **크기가 동적으로 변할 수 있음**

```java
package collection.array;  
  
import java.util.Arrays;  
  
public class MyArrayListV3 {  
  
    private static final int DEFAULT_CAPACITY = 5;  
  
    private Object[] elementData;  
    private int size = 0;  
  
    public MyArrayListV3() {  
        elementData = new Object[DEFAULT_CAPACITY];  
    }  
  
    public MyArrayListV3(int initialCapacity) {  
        elementData = new Object[initialCapacity];  
    }  
  
    public int size() {  
        return size;  
    }  
  
    public void add(Object e) {  
        //코드 추가  
        if (size == elementData.length) {  
            grow();  
        }  
        elementData[size] = e;  
        size++;  
    }  
  
    // 코드 추가  
    public void add(int index, Object e) {  
        //코드 추가  
        if (size == elementData.length) {  
            grow();  
        }  
        //데이터 이동  
        shiftRightFrom(index);  
        elementData[index] = e;  
        size++;  
    }  
  
    // 코드 추가, 요소의 마지막부터 index까지 오른쪽으로 밀기  
    private void shiftRightFrom(int index) {  
        for (int i = size; i > index; i--) {  
            elementData[i] = elementData[i - 1];  
        }    }  
  
    // 코드 추가  
    public Object remove(int index) {  
        Object oldValue = get(index);  
        shiftLeftFrom(index);  
  
        size--;  
        elementData[size] = null;  
        return oldValue;  
  
    }  
  
    // 코드 추가, index부터 마지막까지 왼쪽으로 밀기  
    private void shiftLeftFrom(int index) {  
        for (int i = index; i < size - 1; i++) {  
            elementData[i] = elementData[i + 1];  
  
        }    }  
  
  
    // 코드 추가  
    private void grow() {  
        int oldCapacity = elementData.length;  
        int newCapacity = oldCapacity * 2;  
        // 배열을 새로 만들고, 기존 배열을 새로운 배열에 복사  
        elementData = Arrays.copyOf(elementData, newCapacity);  
    }  
  
    public Object get(int index) {  
        return elementData[index];  
    }  
  
    public Object set(int index, Object element) {  
        Object oldValue = get(index);  
        elementData[index] = element;  
        return oldValue;  
    }  
  
    public int indexOf(Object o) {  
        for (int i = 0; i < size; i++) {  
            if (o.equals(elementData[i])) {  
                return i; // 찾으면 index 번호 반환  
            }  
        }  
        return -1; // 없으면 -1    }  
  
    public String toString() {  
        // [1,2,3,null,null] = size = 3  
        // [1,2,3] = size = 3        return Arrays.toString(Arrays.copyOf(elementData, size)) +  
                " size= " + size + ", capacity= " + elementData.length;  
    }  
  
}
```

```java
package collection.array;  
  
public class MyArrayListV3Main {  
    public static void main(String[] args) {  
        MyArrayListV3 list = new MyArrayListV3();  
  
        //마지막에 추가 O(1)        
        list.add("a");  
        list.add("b");  
        list.add("c");  
        System.out.println(list);  
  
        //원하는 위치에 추가  
        System.out.println("add Last");  
        list.add(3, "addList"); // O(1)  
        System.out.println(list);  
  
        System.out.println("add First");  
        list.add(0, "addFirst"); // O(n)  
        System.out.println(list);  
  
        //삭제  
        Object removed1 = list.remove(4); // remove Last O(1)  
        System.out.println("removed(4) = " + removed1);  
        System.out.println(list);  
  
        Object removed2 = list.remove(0); // remove First O(n)  
        System.out.println("remove(0) = " + removed2);  
        System.out.println(list);  
  
  
  
    }  
}
```

## 배열 리스트(ArrayList)의 빅오
- 데이터 추가
	- 마지막에 추가: O(1)
	- 앞, 중간에 추가: O(n)
- 데이터 삭제
	- 마지막에 삭제: O(1)
	- 앞, 중간에 삭제: O(n)
- 인덱스 조회: O(1)
- 데이터 검색: O(n)

> [!success] 배열 리스트는 이럴 때 좋아요 !
> - 마지막에 데이터를 추가하거나, 삭제할 때는 **O(1)** 로 매우 빠르다.
> - 중간에 데이터를 추가하거나 삭제하는 경우 **O(n)** 으로 느리다.
> - **데이터를 순서대로 입력하고(데이터를 마지막에 추가하고), 
>   순서대로 출력하는 경우에 가장 효율적이다.**

> [!warning] ArrayList의 단점
> - **정확한 크기를 미리 알지 못하면 메모리가 낭비된다.**
> 	- 배열을 사용하므로 배열 뒷 부분에 사용되지 않고, 낭비되는 메모리가 있다.
> - **데이터를 중간에 추가하거나 삭제할 때 비효율적이다.**
> 	- 이 경우 데이터를 한 칸씩 밀어야 한다. O(n)의 성능으로 좋지 않다.
> 	- 만약 데이터의 크기가 1,000,000건이라면, 최악의 경우 데이터를 추가할 때마다 1,000,000건의 데이터를 밀어야 한다.



배열 리스트는 내부에 배열을 사용해서 데이터를 보관하고 관리해서
사용하지 않은 공간이 낭비되는 단점이 있다.
(배열은 필요한 배열의 크기를 미리 확보해야 해서 데이터가 얼마나 추가될지 예측할 수 없는 경우 나머지 공간은 사용되지 않고 낭비된다.)

<<배열 리스트의 단점을 극복하는 방법>>
[[Collection - 3) LinkedList]]
