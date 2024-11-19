# 노드와 연결 1
>[!warning] 배열 리스트의 단점
>배열 리스트는 내부에 배열을 사용해서 데이터를 보관하고 관리한다.
>1. **배열은 필요한 배열의 크기를 미리 확보해야 하기 때문에** 데이터가 얼마나 추가될지 예측할 수 없는 경우 **나머지 공간은 사용되지 않고 낭비된다.**
>2. 배열의 앞이나 중간에 데이터를 추가하면 추가할 데이터의 공간을 확보하기 위해
>   기존 데이터들을 오른쪽으로 이동해야 한다. O(n)
>   그리고 삭제의 경우에는 빈 공간을 채우기 위해 왼쪽으로 이동해야 한다.
>   **이렇게 앞이나 중간에 데이터를 추가하거나 삭제하는 경우 성능이 좋지 않다.**

![[Pasted image 20241113153810.png]]
![[Pasted image 20241113154004.png]]


>[!success] 노드와 연결
>**낭비되는 메모리 없이 딱 필요한 만큼만 메모리를 확보해서 사용**하고,
>**앞이나 중간에 데이터를 추가하거나 삭제할 때도 효율적인 자료구조**가 있는데,
>노드를 만들고, 각 노드를 서로 연결하는 방식이다.

```java
package link;  
  
public class Node {  
  
    Object item; // 저장할 데이터
    Node next; // 다음으로 연결할 노드의 참조
  
    public Node(Object item) {  
        this.item = item;  
    }  
}
```
```java
package link;  
  
public class NodeMain1 {  
    public static void main(String[] args) {  
        // 노드 생성하고 연결하기 A -> B -> C 
        Node first = new Node("A");  
        first.next = new Node("B");  
        first.next.next = new Node("C");  
  
        System.out.println("모든 노드 탐색하기");  
        Node x = first;  
        while (x != null) {  
            System.out.println(x.item);  
            x = x.next;  
        }    }  
}
```

![[Pasted image 20241113154408.png]]


# 노드와 연결 2
```java
package link;  
  
public class Node {  
  
    Object item;  
    Node next;  
  
    public Node(Object item) {  
        this.item = item;  
    }  
  
    @Override  
    public String toString() {  
        StringBuilder sb = new StringBuilder();  
        Node x = this;  
        sb.append("[");  
        while (x != null) {  
            sb.append(x.item);  
            if (x.next != null) {  
                sb.append("->");  
            }  
            x = x.next;  
        }        sb.append("]");  
        return sb.toString();  
    }  
}
```
```java
package link;  
  
public class NodeMain2 {  
    public static void main(String[] args) {  
        // 노드 생성하고 연결하기 A -> B -> C        Node first = new Node("A");  
        first.next = new Node("B");  
        first.next.next = new Node("C");  
  
        System.out.println("연결된 노드 출력하기");  
        System.out.println(first);  
        System.out.println(first.next);  
    }  
}
```
```
연결된 노드 출력하기
[A->B->C]
[B->C]
```

# 노드와 연결 3
==노드와 연결을 활용해서 다양한 기능을 만들어보자.==
```java
package link;  
  
public class NodeMain3 {  
    public static void main(String[] args) {  
        //노드 생성하고 연결하기: A -> B -> C  
        Node first = new Node("A");  
        first.next = new Node("B");  
        first.next.next = new Node("C");  
  
        System.out.println(first);  
  
        //모든 노드 탐색하기  
        System.out.println("모든 노트 탐색하기");  
        printAll(first);  
  
        //마지막 노드 조회하기  
        Node lastNode = getLastNode(first);  
        System.out.println("lastNode = " + lastNode);  
  
        //특정 index의 노드 조회하기  
        int index = 2;  
        Node index2Node = getNode(first, index);  
        System.out.println("index2Node = " + index2Node.item);  
  
        //데이터 추가하기  
        System.out.println("데이터 추가하기");  
        add(first, "D");  
        System.out.println(first);  
        add(first, "E");  
        System.out.println(first);  
        add(first, "F");  
        System.out.println(first);  
  
    }  
  
    private static void printAll(Node node) {  
        Node x = node;  
        while (x != null) {  
            System.out.println(x.item);  
            x = x.next;  
        }  
    }  
  
    private static Node getLastNode(Node node) {  
        Node x = node;  
        while (x.next != null) {  
            x = x.next;  
        }        return x;  
  
    }  
  
    private static Node getNode(Node node, int index) {  
        Node x = node;  
  
        for (int i = 0; i < index; i++) {  
  
            x = x.next;  
  
        }        return x;  
  
    }  
  
    private static void add(Node node, String param) {  
        Node lastNode = getLastNode(node);  
        lastNode.next = new Node(param);  
    }  
}
```
```java
A
B
C
lastNode = [C]
index2Node = C
데이터 추가하기
[A->B->C->D]
[A->B->C->D->E]
[A->B->C->D->E->F]
```

>[!info] 정리
>- **노드는 내부에 데이터와 다음 노드에 대한 참조를 가지고 있다.**
>- 지금까지 설명한 구조는 각각의 노드가 참조를 통해 연결(Link, 링크) 되어 있다.
>- **데이터를 추가할 때 동적으로 필요한 만큼의 노드만 만들어서 연결하면 된다.**
>	- 따라서 **배열과 다르게 메모리를 낭비하지 않는다.**
>	- 물론 `next`필드를 통해 참조값을 보관해야 하기 때문에 **배열과 비교해서 추가적인 메모리 낭비도 발생한다.**
>- 이렇게 각각의 **노드를 연결(링크)해서 사용하는 자료 구조**로 리스트를 만들 수 있는데, 이것을 **연결 리스트**라 한다.

# 직접 구현하는 연결 리스트 - 시작
>[!info] 연결리스트의 장점
>연결 리스트는 **배열 리스트의 단점인 메모리 낭비, 중간 위치의 데이터 추가에 대한 성능 문제를
> 어느정도 극복할 수 있다.**

```java
package link;  
  
public class MyLInkedListV1 {  
  
    private Node first;  
    private int size = 0;  
  
    // 마지막에 데이터를 추가한다.  
    // 새로운 노드를 만들고, 마지막 노드를 찾아서 새로운 노드를 마지막에 연결한다.  
    public void add(Object e) {  
        Node newNode = new Node(e);  
        if (first == null) {  
            first = newNode;  
        } else {  
            Node lastNode = getLastNode();  
            lastNode.next = newNode;  
        }        size++;  
    }  
  
    private Node getLastNode() {  
        Node x = first;  
        while (x.next != null) { // node의 next값이 null인 것이 마지막 노드  
            x = x.next;  
        }        return x;  
    }  
  
    // 특정 위치에 있는 데이터를 찾아서 변경하고 기존 값을 반환한다.  
    public Object set(int index, Object element) {  
        Node x = getNode(index);  
        Object oldValue = x.item;  
        x.item = element;  
        return oldValue;  
    }  
  
    // 특정 위치에 있는 데이터를 반환  
    public Object get(int index) {  
        Node node = getNode(index);  
        return node.item;  
    }  
  
    // 특정 위치에 있는 노드를 찾아서 반환한다.  
    private Node getNode(int index) {  
        Node x = first;  
        for (int i = 0; i < index; i++) {  
            x = x.next;  
        }        return x;  
    }  
  
    // 데이터를 검색하고, 검색된 위치를 반환한다.  
    public int indexOf(Object o) {  
        int index = 0;  
        for (Node x = first; x != null; x = x.next) {  
            if (o.equals(x.item)) {  
                return index;  
            }  
            index++;  
        }        return -1;  
    }  
    public int size() {  
        return size;  
    }  
  
    @Override  
    public String toString() {  
        return "MyLInkedListV1{" +  
                "first=" + first +  
                ", size=" + size +  
                '}';  
    }  
}
```

>연결 리스트는 데이터를 추가할 때 마다 동적으로 노드가 늘어나기 때문에 범위를 초과하는 문제는 발생하지 않는다.

## 연결 리스트와 빅오
![[Pasted image 20241113171540.png]]
- `Object get(int index)`
	- 특정 위치에 있는 데이터를 반환한다.
	- O(n)
		- 배열은 인덱스로 원한느 데이터를 즉시 찾을 수 있다.
		- 따라서 배열을 사용하는 **배열 리스트(ArrayList)는 인덱스로 조회 시 O(1)의 빠른 성능을 보장한다.**
		- 연결리스트에서 사용하는 노드들은 배열이 아니라 단지 다음 노드에 대한 참조가 있을 뿐이다. 따라서 **인덱스로 원하는 위치의 데이터를 찾으려면 인덱스 숫자 만큼 다음 노드를 반복해서 찾아야 한다.**
		- 따라서 **인덱스 조회 성능이 나쁘다.**
- `void add(Object e)`
	- 마지막에 데이터를 추가한다.
	- O(n)
		- **마지막 노드를 찾는데 O(n)이 소요된다.**
		- 마지막 노드에 새로운 노드를 추가하는데 O(1)이 걸린다.
		- **따라서 O(n)이다.**
- `Object set(int index, Object element)`
	- 특정 위치에 있는 데이터를 찾아서 변경한다. 그리고 기존 값을 반환한다.
	- O(n)
		- 특정 위치의 노드를 찾는데 O(n)이 걸린다.
- `int indexOf(Object o)`
	- 데이터를 검색하고, 검색된 위치를 반환한다.
	- O(n)
		- 모든 노드를 순회하면서 `equals()`를 사용해서 같은 데이터가 있는지 찾는다.


>[!Note] 정리
>연결 리스트를 통해 데이터를 추가하는 방식은 **꼭 필요한 메모리만 사용한다.**
>따라서 배열 리스트의 단점인 **메모리 낭비를 해결할 수 있다.**
>물론, 연결을 유지하기 위한 **추가 메모리가 사용되는 단점도 함께 존재한다.**


# 직접 구현하는 연결 리스트 - 추가와 삭제
```java
//추가 메서드: 특정 위치에 데이터를 추가하고, 내부에서 노드도 추가한다.
public void add(int index, Object e) {  
    Node newNode = new Node(e);  
    if (index == 0) { // 맨앞에 추가  
        newNode.next = first; // 새로운 노드의 next는 기존 노드를 가리킨다.
        first = newNode;  
    } else {// 중간에 추가  
        Node prev = getNode(index - 1); // 노드가 입력될 위치의 직전 노드를 찾는다.
        newNode.next = prev.next; 
        prev.next = newNode;  
    }    size++;  
}
```


## 첫번째 위치에 데이터 추가
![[Pasted image 20241113185643.png]]

![[Pasted image 20241113190017.png]]
- 노드를 추가했으므로 오른쪽 노드의 index가 하나씩 뒤로 밀려난다.
	- 연결 리스트는 배열처럼 **실제 index가 존재하는 것이 아니다.**
	- 처음으로 연결된 노드를 index 0, 그 다음으로 연결된 노드를 index 1로 가정할 뿐이다.
	- **연결리스트에서 index는 연결된 순서를 뜻한다.**
- 배열의 경우 첫 번째 항목에 데이터가 추가되면 모든 데이터를 오른쪽으로 하나씩 밀어야 하지만, **연결리스트는 새로 생성한 노드의 참조만 변경하면 된다.**
- 연결 리스트의 첫번째 항목에 값을 추가하는 것은 매우 빠르다. O(1)로 표현할 수 있다.


## 중간 위치에 데이터 추가
1. 새로운 노드를 생성하고, 노드가 입력될 위치의 직전 노드(prev)를 찾아둔다.
![[Pasted image 20241113191026.png]]
![[Pasted image 20241113191114.png]]
![[Pasted image 20241113191406.png]]
- 노드를 추가했으므로 추가한 노드 오른쪽에 있는 노드들의 index가 하나씩 뒤로 밀려난다.
- 배열의 경우 데이터가 추가되면 인덱스 위치 부터 모든 데이터를 오른쪽으로 하나씩 밀어야 하지만, **연결리스트는 새로 생성한 노드의 참조만 변경하면 된다.**
- O(n)의 성능이다.
	- 연결 리스트는 인덱스를 사용해서 **노드를 추가할 위치를 찾는데 O(n)이 걸린다.**
	- 위치를 찾고 노드를 추가하는데는 O(1)이 걸린다.
	- 따라서 둘을 합하면 O(n)이 걸린다.


```java
//삭제 메서드: 특정 위치에 있는 데이터를 삭제하고, 내부에서 노드도 함께 제거한다.
public Object remove(int index) {  
    Node removeNode = getNode(index); // 제거할 대상의 노드를 가져온다.
    Object removedItem = removeNode.item;  
  
    if (index == 0) { // 맨 앞 노드 삭제  
        first = removeNode.next;//대상 노드를 삭제하면,이 노드의 다음이 첫번째가 된다.
    } else { // 중간 노드 삭제  
        Node prev = getNode(index - 1);  
        prev.next = removeNode.next;  
    }  
    
    // 3. 삭제 대상의 데이터 초기화
    removeNode.item = null;  
    removeNode.next = null;
      
    size--;  
    
    return removedItem;  
}
```

## 첫번째 위치에 데이터 삭제
![[Pasted image 20241113190308.png]]
![[Pasted image 20241113190326.png]]
- 노드를 삭제했으므로 오른쪽 노드의 index가 하나씩 당겨진다.
- 배열의 경우 첫번째 항목이 삭제되면 모든 데이터를 왼쪽으로 하나씩 밀어야 하지만
  연결리스트는 일부 참조만 변경하면 된다. 나머지 노드는 어떤 일이 일어난지도 모른다.
- 연결 리스트의 첫번째 항목에 값을 삭제하는 것은 매우 빠르다. O(1)로 표현할 수 있다.

## 중간 위치에 데이터 삭제
![[Pasted image 20241113191840.png]]
3. 삭제 노드의 데이터를 초기화 한다.
![[Pasted image 20241113191904.png]]
- 노드를 삭제했으므로 오른쪽 노드의 index가 하나씩 당겨진다.
- O(n)의 성능이다.
	- 연결리스트에서 인덱스로 **삭제할 항목을 찾는데 O(n)이 걸린다.**
	- 연결리스트에서 항목을 삭제하는 것은 매우 빠르다. O(1)로 표현할 수 있다.

>연결리스트와 배열리스트 둘다 중간에 있는 항목을 추가하거나 삭제하는 경우
>둘 다 같은 O(n)이다.


| 기능        | 배열 리스트 | 연결 리스트 |
| --------- | ------ | ------ |
| 인덱스 조회    | O(1)   | O(n)   |
| 검색        | O(n)   | O(n)   |
| 앞에 추가(삭제) | O(n)   | O(1)   |
| 뒤에 추가(삭제) | O(1)   | O(n)   |
| 평균 추가(삭제) | O(n)   | O(n)   |
>[!info] 배열 리스트 VS 연결 리스트 사용
>데이터를 조회할 일이 많고, 뒷 부분에 데이터를 추가한다면 배열 리스트가 보통 더 좋은 성능을 제공한다.
>앞쪽의 데이터를 추가하거나 삭제할 일이 많다면 연결 리스트를 사용하는 것이 보통 더 좋은 성능을 제공한다.

> [!tip] 참고: 단일 연결 리스트, 이중 연결 리스트
> 우리가 구현한 연결 리스트는 한 방향으로만 이동하는 단일 연결 리스트다.
> 노드를 앞뒤로 모두 연결하는 이중 연결 리스트를 사용하면 성능을 더 개선할 수 있다.
> **자바가 제공하는 연결 리스트는 이중 연결 리스트다.**
> 추가로 자바가 제공하는 연결 리스트는 마지막 노드를 참조하는 변수를 가지고 있어서, **뒤에 추가하거나 삭제하는 경우에도 O(1)의 성능을 제공한다.**






# 직접 구현하는 연결 리스트 - 제네릭 도입
```java
package collection.link;  
  
public class MyLinkedListV3<E> {  
    private Node<E> first;  
    private int size = 0;  
  
    public void add(E e) {  
  
        Node<E> newNode = new Node<>(e);  
  
        if (first == null) {  
            first = newNode;  
        } else {  
  
            Node<E> lastNode = getLastNode();  
  
            lastNode.next = newNode;  
  
        }        size++;  
  
    }  
  
    private Node<E> getLastNode() {  
  
        Node<E> x = first;  
  
        while (x.next != null) {  
            x = x.next;  
        }        return x;  
  
    }  
  
    public void add(int index, E e) {  
        Node<E> newNode = new Node<>(e);  
  
        if (index == 0) {  
            newNode.next = first;  
            first = newNode;  
        } else {  
            Node<E> prev = getNode(index - 1);  
            newNode.next = prev.next;  
            prev.next = newNode;  
        }        size++;  
  
    }  
  
    public E set(int index, E element) {  
  
        Node<E> x = getNode(index);  
  
        E oldValue = x.item;  
        x.item = element;  
        return oldValue;  
  
    }  
  
    public E remove(int index) {  
  
        Node<E> removeNode = getNode(index);  
  
        E removedItem = removeNode.item;  
        if (index == 0) {  
  
            first = removeNode.next;  
        } else {  
  
            Node<E> prev = getNode(index - 1);  
  
            prev.next = removeNode.next;  
        }        removeNode.item = null;  
        removeNode.next = null;  
        size--;  
        return removedItem;  
  
    }  
  
    public E get(int index) {  
  
        Node<E> node = getNode(index);  
  
        return node.item;  
  
    }  
  
    private Node<E> getNode(int index) {  
  
        Node<E> x = first;  
  
        for (int i = 0; i < index; i++) {  
  
            x = x.next;  
        }        return x;  
  
    }  
  
    public int indexOf(E o) {  
  
        int index = 0;  
        for (Node<E> x = first; x != null; x = x.next) {  
  
            if (o.equals(x.item))  
  
                return index;  
  
            index++;  
        }        return -1;  
  
    }  
  
    public int size() {  
        return size;  
    }  
  
    @Override  
    public String toString() {  
        return "MyLinkedListV3{" +  
  
                "first=" + first +  
  
                ", size=" + size +  
  
                '}';  
  
    }  
  
    private static class Node<E> {  
        E item;  
        Node<E> next;  
  
        public Node(E item) {  
            this.item = item;  
        }  
        @Override  
        public String toString() {  
            StringBuilder sb = new StringBuilder();  
  
            Node<E> temp = this;  
  
            sb.append("[");  
            while (temp != null) {  
  
                sb.append(temp.item);  
                if (temp.next != null) {  
                    sb.append("->");  
                }  
                temp = temp.next;  
  
            }  
            sb.append("]");  
            return sb.toString();  
  
        }  
    }  
  
}
```