
데이터를 변경하는 경우에는 ArrayList 보다 LinkedList가 더 빠르다 ?

![[Pasted image 20250527172037.png]]

- 앞에 추가(삭제)하는 경우라면 OK
- 하지만, 나머지 경우라면 압도적으로 ArrayList가 20배 정도 더 빠르다.
- Why ?
	- 요소의 순차적 접근 속도, 메모리 할당 및 해제 비용, CPU 캐시 활용도 등에 영향을 받는다.
	- ArrayList는 요소들이 메모리 상에서 연속적으로 위치해서 CPU 캐시 효율이 좋고, 메모리 접근 속도가 빠르다.
	- 반면 LinkedList는 각 요소가 별도의 객체로 존재하고, 다음 요소의 참조를 저장하기 때문에 CPU 캐시 효율이 떨어지고, 메모리 접근 속도가 상대적으로 느릴 수 있다.
	- ArrayList의 경우 CAPACITY를 넘어서면 배열을 다시 만들고 복사하는 과정이 추가된다. 하지만 한번에 50%씩 늘어나기 때문에 이 과정은 가끔 발생하므로 전체 성능에 큰 영향을 주지 않는다. 그리고 `System.arraycopy()` 사용 시 **메모리 블록 복사**로 배열을 덩어리 째 복사하므로 성능이 좋다.

인스턴스를 실행 시점에 넘기는 것 = Dependency Injection

**의존 관계 주입(DI)** 은 클라이언트 코드가 사용할 리스트의 구체적인 타입을 코딩 시점이 아닌 **실행 시점(런타임)에 결정**하도록 해줍니다. 이는 클라이언트 코드를 수정하지 않고도 동작 방식을 변경할 수 있게 합니다.

