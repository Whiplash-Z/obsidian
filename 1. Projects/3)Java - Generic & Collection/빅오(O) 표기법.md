---
tags:
  - 자료구조
---


>[!info] 빅오(O) 표기법이란?
>알고리즘의 성능을 분석할 때 사용하는 수학적 표현 방식이다.
>- 특히 알고리즘이 처리해야 할 데이터의 양이 증가할 때, 그 알고리즘이 얼마나 빠르게 실행되는지 나타낸다.
>- 알고리즘의 정확한 실행 시간을 계산하는 것이 아니라, 데이터 양의 증가에 따른 성능의 변화 추세를 이해하는 것이 중요하다.
![[Pasted image 20241111194447.png]]
![[Pasted image 20241111195056.png]]

```java
package collection.array;  
  
import java.util.Arrays;  
  
public class ArrayMain2 {  
    public static void main(String[] args) {  
        int[] arr = new int[5];  
        arr[0] = 1;  
        arr[1] = 2;  
        System.out.println(Arrays.toString(arr));  
  
        // 배열의 첫번째 위치에 추가  
        // 기본 배열의 데이터를 한 칸식 뒤로 밀고 배열의 첫번째 위치에 추가  
        System.out.println("배열의 첫번째 위치에 3 추가 O(n)");  
        int newValue = 3;  
        addFirst(arr, newValue);  
        System.out.println(Arrays.toString(arr));  
  
        // index 위치에 추가  
        // 기본 배열의 데이터를 한 칸씩 뒤로 밀고 배열의 index 위치에 추가  
        System.out.println("배열의 index(2) 위치에 4 추가 O(n)");  
        int index = 2;  
        int value = 4;  
        addAtIndex(arr, index, value);  
        System.out.println(Arrays.toString(arr));  
  
        System.out.println("배열의 마지막 위치에 5 추가 O(1)");  
        addLast(arr, 5);  
        System.out.println(Arrays.toString(arr));  
  
    }  
  
    private static void addLast(int[] arr, int newValue) {  
        arr[arr.length - 1] = newValue;  
    }  
  
  
    private static void addAtIndex(int[] arr, int index, int newValue) {  
        for (int i = arr.length - 1; i > index; i--) {  
            arr[i] = arr[i - 1];  
        }        arr[index] = newValue;  
    }  
  
    private static void addFirst(int[] arr, int newValue) {  
        for (int i = arr.length - 1; i > 0; i--) {  
            arr[i] = arr[i - 1];  
        }        arr[0] = newValue;  
    }  
}


```

```java
[1, 2, 0, 0, 0]
배열의 첫번째 위치에 3 추가 O(n)
[3, 1, 2, 0, 0]
배열의 index(2) 위치에 4 추가 O(n)
[3, 1, 4, 2, 0]
배열의 마지막 위치에 5 추가 O(1)
[3, 1, 4, 2, 5]
```