
데이터를 추가할 때도, 중복 데이터가 있는지 전체 탐색해야 하므로 O(n) -> 성능이 좋지 않은데..? -> 해시 알고리즘으로 해결이 된다고 !?

# 시작

>[!Success] 해시(hash) 알고리즘을 사용하면 데이터를 찾는 검색 성능을 평균 O(1)로 비약적으로 끌어올릴 수 있다.

```java
public class HashStart1 {  
    public static void main(String[] args) {  
        Integer[] inputArray = new Integer[4];  
        inputArray[0] = 1;  
        inputArray[1] = 2;  
        inputArray[2] = 5;  
        inputArray[3] = 8;  
        System.out.println("inputArray = " + Arrays.toString(inputArray));  
  
        int searchValue = 8;  
        // 4번 반복 -> O(n)
        for (Integer inputValue : inputArray) {  
            if (inputValue == searchValue) {  
                System.out.println(inputValue);  
            }  
        }  
    }  
}
```

# index 사용


![[Pasted image 20250527201809.png]]

```java
public class HashStart2 {  
    public static void main(String[] args) {  
        // 입력: 1,2,5,8  
        // [null, 1, 2, null, null, 5, null, null, 8, null]        Integer[] inputArray = new Integer[10];  
        inputArray[1] = 1;  
        inputArray[2] = 2;  
        inputArray[5] = 5;  
        inputArray[8] = 8;  
        System.out.println("inputArray = " + Arrays.toString(inputArray));  
  
        int searchValue = 8;  
        Integer result = inputArray[searchValue]; // O(1)  
        System.out.println("result = " + result);  
    }  
}
```

>[!Success] O(n)의 검색 성능을 O(1)로 획기적으로 개선할 수 있다.
>데이터의 값 자체를 배열의 인덱스로 사용해서

>[!bug] 배열에 낭비되는 공간이 많이 발생한다.
>입력 값의 범위 만큼 큰 배열을 사용해야 하기 때문에


# 메모리 낭비

```java
public class HashStart3 {  
    public static void main(String[] args) {  
        // 입력: 1,2,5,8, 14, 99}  
        // [null, 1, 2, null, null, 5, null, null, 8, ..., 14, ..., 99]        Integer[] inputArray = new Integer[100];  
        inputArray[1] = 1;  
        inputArray[2] = 2;  
        inputArray[5] = 5;  
        inputArray[8] = 8;  
        inputArray[14] = 14;  
        inputArray[99] = 99;  
        System.out.println("inputArray = " + Arrays.toString(inputArray));  
  
        int searchValue = 99;  
        Integer result = inputArray[searchValue];  
        System.out.println("result = " + result);  
    }  
}
```

>[!Warning] 한계
>- 데이터의 값을 인덱스로 사용한 덕분에 O(1)의 매우 빠른 검색 속도를 얻을 수 있다.
>- 하지만 낭비되는 공간이 너무 많다.
>- 만약 int, long, double 등의 범위를 감당하려면..? -> ❌❌❌
>- int (4byte * 42억(-21억 ~ 21억) = 17GB필요)

# 나머지 연산
>[!info] 해시 인덱스(hashIndex)
>배열의 인덱스로 사용할 수 있도록 원래의 값을 계산한 인덱스
>- 14의 해시 인덱스 = 4 (value % CAPACITY)
>- 99의 해시 인덱스 = 9 (value % CAPACITY)

## 해시 인덱스와 데이터 저장

![[Pasted image 20250527205203.png]]

- 해시 인덱스를 배열의 인덱스로 사용해서 데이터를 저장한다.
	- `inputArray[hashIndex] = value`
	- 인덱스만 해시 인덱스를 사용하고, 값은 원래 값을 저장한다.
- 배열의 인덱스를 사용하기 때문에 하나의 값을 저장하는데 O(1)로 빠른 성능을 제공한다.
	- 해시 인덱스 생성 O(1) + 해시 인덱스를 사용해서 배열에 값 저장 O(1) => O(1)


## 해시 인덱스와 데이터 조회
![[Pasted image 20250527205625.png]]

- 조회할 값에 나머지 연산자를 사용해서 해시 인덱스를 구한다.
	- 2 % 10 = 2
	- 14 % 10 = 4
	- 99 % 10 = 9
- 해시 인덱스를 배열의 인덱스로 사용해서 데이터를 조회한다.
	- `int value = inputArray[hashIndex]`
		- 14를 찾아라 -> `14 % 10 -> inputArray[4]` => 14
	- 인덱스만 해시 인덱스를 사용하고, 값은 원래 값을 조회한다.
- 배열의 인덱스를 사용하기 때문에 하나의 값을 찾는데 O(1)로 성능을 제공한다.
	- 해시 인덱스 생성 O(1) + 해시 인덱스를 사용해서 배열에 값 저장 O(1) => **O(1)**

## 코드 구현
```java
public class HashStart4 {  
    static final int CAPACITY = 10;  
  
    public static void main(String[] args) {  
        // {1, 2, 5, 8, 14, 99}  
        System.out.println("hashIndex(1) = " + hashIndex(1));  
        System.out.println("hashIndex(2) = " + hashIndex(2));  
        System.out.println("hashIndex(5) = " + hashIndex(5));  
        System.out.println("hashIndex(8) = " + hashIndex(8));  
        System.out.println("hashIndex(14) = " + hashIndex(14));  
        System.out.println("hashIndex(99) = " + hashIndex(99));  
  
        Integer[] inputArray = new Integer[CAPACITY];  
        add(inputArray, 1);  
        add(inputArray, 2);  
        add(inputArray, 5);  
        add(inputArray, 8);  
        add(inputArray, 14);  
        add(inputArray, 99);  
        System.out.println("Arrays.toString(inputArray) = " + Arrays.toString(inputArray));  
        // 검색  
        int searchIndex = 14;  
        int hashIndex = hashIndex(searchIndex);  
        int result = inputArray[hashIndex];  
        System.out.println("result = " + result);  
    }  
  
    private static void add(Integer[] inputArray, int value) {  
        int hashIndex = hashIndex(value);  
        inputArray[hashIndex] = value;  
    }  
  
    static int hashIndex(int value) {  
        return value % CAPACITY;  
    }  
  
}
```
```
hashIndex(1) = 1
hashIndex(2) = 2
hashIndex(5) = 5
hashIndex(8) = 8
hashIndex(14) = 4
hashIndex(99) = 9
Arrays.toString(inputArray) = [null, 1, 2, null, 14, 5, null, null, 8, 99]
result = 14
```


>[!Success] 입력 값의 범위가 넓어도 실제 모든 값이 들어오지 않기 때문에 배열의 크기를 제한하고, 나머지 연산(%)을 통해 메모리가 낭비되는 문제도 해결할 수 있다.

-> 해시 인덱스를 통해서 O(1)의 성능으로 데이터를 조회할 수 있게 되었다.
덕분에 자료구조의 조회 속도를 비약적으로 향상할 수 있게 되었다.

>[!Warning] 한계
>- 저장할 위치가 충돌할 수 있는 한계가 있다.
>- 1, 11과 같은 값은 나머지가 똑같다.
>- 값이 덮어져 기존의 데이터가 날아갈 위험이 생겼다.

# 해시 충돌 설명
>[!Info] 해시 충돌
>99, 9의 두 값은 10으로 나누면 9가 된다.
>**다른 값**을 입력했지만 **같은 해시 코드**가 나오게 되는 것을 의미한다.

![[Pasted image 20250527215128.png]]


# 해시 충돌 해결
>[!Success] 해시 충돌은 낮은 확률로 일어날 수 있다고 가정한다.
>해결 방안: 해시 충돌이 일어났을 때 단순하게 같은 해시 인덱스의 값을 같은 인덱스에 함께 저장해버리는 것이다.

![[Pasted image 20250527215922.png]]

- 물론 여러 데이터를 배열 하나의 공간에 함께 저장할 수는 없다.
	- 대신 **배열 안에 배열**을 만들면 된다.
	- 물론 배열 안에 리스트 같은 다른 자료구조를 사용해도 된다.

![[Pasted image 20250527220525.png]]

- 비교시 equals를 사용했지만 기본형이라면 == 을 사용해도 된다.

## 최악의 경우
![[Pasted image 20250527220637.png]]
- 이처럼 모든 값의 해시 인덱스가 9인 최악의 경우 **O(n) 의 성능을 보인다.**


# 정리
- 해시 인덱스를 사용하는 방식은 최악의 경우 O(n)의 성능을 보인다.
- 하지만 **확률적으로** 보면 어느정도 넓게 퍼지기 때문에 **평균으로 보면 대부분 O(1)의 성능**을 제공한다.
- 해시 충돌이 가끔 발생해도 내부에서 값을 몇 번만 비교하는 수준이기 때문에 대부분의 경우 매우 빠르게 값을 찾을 수 있다.


# 코드 구현
```java
public class HashStart5 {  
    static final int CAPACITY = 10;  
  
    public static void main(String[] args) {  
        // {1, 2, 5, 8, 14, 99}  
        LinkedList<Integer>[] buckets = new LinkedList[CAPACITY];  
        // LinkedList를 사용한 이유: Array를 쓰면 메모리 공간을 미리 확보하기 때문, 충돌이 날 확률은 아주 낮다고 가정했기 때문에, 충돌이 났을 때 메모리 공간을 만들어주기 위한 목적 -> 메모리 효율성 고려
        System.out.println("buckets = " + Arrays.toString(buckets));  
  
        for (int i = 0; i < CAPACITY; i++) {  
            buckets[i] = new LinkedList<>();  
        }  
        System.out.println("Arrays.toString(buckets) = " + Arrays.toString(buckets));  
        add(buckets, 1);  
        add(buckets, 2);  
        add(buckets, 5);  
        add(buckets, 8);  
        add(buckets, 14);  
        add(buckets, 9);  
        add(buckets, 99);  
        add(buckets, 99); // 같은 값이 들어가지 않음
        System.out.println("Arrays.toString(buckets) = " + Arrays.toString(buckets));  
  
        // 검색  
        int searchValue = 14;  
        boolean contains = contains(buckets, searchValue);  
        System.out.println("buckets.contains(" + searchValue + ") = " + contains);  
  
  
    }  
  
    private static void add(LinkedList<Integer>[] buckets, int value) {  
        // 1. HashIndex 구하기.  
        int hashIndex = hashIndex(value);  
        LinkedList<Integer> bucket = buckets[hashIndex];  
        if (!bucket.contains(value)) { // 같은 값이 두 번 이상 안 들어가도록 제약조건 추가. (SET -> 중복 허용 X)  O(n)  
           bucket.add(value);  
        }  
    }  
  
    private static int hashIndex(int value) {  
        return value % CAPACITY;  
    }  
  
    private static boolean contains(LinkedList<Integer>[] buckets, int searchValue) {  
        int hashIndex = hashIndex(searchValue);  
        LinkedList<Integer> bucket = buckets[hashIndex]; // O(1)  
        return bucket.contains(searchValue); // O(n)  
    }  
}
```

- 연결 리스트(LinkedList)의 `contains()`는 **모든 항목을 다 순회**하기 때문에 **O(n)의 성능**이다.
- 하지만 **해시 충돌이 발생하지 않으면** 데이터가 1개만 들어있기 때문에 **O(1)의 성능**을 제공한다.
- **해시 충돌이 발생하면** 데이터를 추가하거나 조회할 때, 연결 리스트 내부에서 O(n)의 추가 연산(contains())을 해야하므로 성능이 떨어진다.
- 해시 충돌이 발생할 확률은 입력하는 데이터의 수와 배열의 크기와 관련이 있다.
	- 입력하는 데이터의 수와 비교해서 배열의 크기가 클 수록 충돌 확률은 낮아진다.
	- 데이터의 수 / 배열의 크기 = 충돌 확률
	![[Pasted image 20250527230908.png]]
	- 데이터의 수가 배열의 크기를 **75%** 넘지 않으면 해시 인덱스는 **자주 충돌하지 않는다.**
	- 배열의 크기를  크게 만들면 해시 충돌은 줄어서 성능은 좋아지지만, 많은 메모리가 낭비된다.
	- 반대로 배열의 크기를 너무 작게 만들면 해시가 자주 충돌해서 성능이 나빠진다.
	- 따라서 상황에 따라 75%를 적절한 크기로 보고 기준으로 잡는 것이 효과적이다.

### 정리
해시 인덱스를 사용하는 경우
- 데이터 저장 -> 중복되는 값이 있는지 확인
	- 평균: O(1)
	- 최악: O(n)
- 데이터 조회 -> 모든 항목을 다 순회
	- 평균: O(1)
	- 최악: O(n)
- 해시 인덱스를 사용하는 방식은 **사실 최악의 경우는 거의 발생하지 않는다.**
	- 배열의 크기만 적절하게 잡아주면 대부분 O(1)에 가까운 매우 빠른 성능을 보여준다.