## Stream이란?

Java8부터 지원하며 컬렉션, 배열 등에 저장되어 있는 요소들을 하나씩 참조하며 반복적인 처리를 가능하게 하는 기능이다. 즉, 함수형 인터페이스인 **람다(lambda)**를 활용할 수 있는 기술이다.

Stream 이용 시, 불필요한 for문이나 if분 등 분기처리를 하지 않고도 깔끔하고 직관적인 코드를 변형할 수 있다.

------

## 특징

1. **데이터를 담고 있는 저장소가 아니다.**
2. **Stream은 데이터를 변경하지 않는다.**

- 원본데이터로부터 읽기만 할 뿐, 변경하지 않는다

1. **Stream은 일회용이다.**

- 한 번 사용하면 닫혀서 재사용이 불가하다. 필요하다면 정렬된 결과를 컬렉션이나 배열에 담아 반환할 수 있다

1. **최종 연산 전까지 중간연산을 수행하지 않는다.**
2. **Stream은 작업을 내부 반복으로 처리한다.**

- 내부 반복은 반복문을 메서드의 내부에 숨길 수 있다는 것으로, 코드상에 노출되지 않아 간결한 작업이 가능하다.

1. 손쉽게 병렬 처리할 수 있다.

------

## Stream 구조

크게 **Stream 생성 → 중개 연산 → 최종 연산** 의 단계로 동작한다.

세 가지로 구성되며 중개 연산은 연산 결과를 Stream형태로 반환하기 때문에 연속적으로 연결해서 사용할 수 있다. 즉, **데이터소스객체집합.Stream생성().중개연산().최종연산();** 와 같은 형태이다.

```java
String[] intArray = {"hi", "good", "music", "purple", "iphone"};
Set<String> set = Arrays.asList(intArray)
								.stream()
								.filter(e->E
```

- 생성(Stream Source)
  - 컬렉션이나 배열 등으로부터 스트림을 생성하는 작업
- 중간 연산(Intermediate Operations)
  - 스트림을 필터링하거나 요소를 알맞게 변환하는 작업
- 단말 연산(Terminal Operations)
  - 최종적으로 결과를 도출하는 작업

------

### Stream 생성

기본적으로 컬렉션 구현 클래스의 Stream 메소드를 이용해 생성하거나 배열로 생성이 가능하다.

- 컬렉션(Collection)으로 생성 (→ `Stream` 메소드 사용)

```java
List<String> list = List.of("hi","hello");
Stream<String> stream =list.stream();
```

- 배열(Array)로 생성 (→ `Arrays.stream` 메소드 사용)

```java
String[] arr = new String[]{"hi", "hello"};
Stream<String> stream = Arrays.stream(arr);

Stream<String> specficStream =Array.stream(arr, 0, 1);

specificStream.forEach(System.out::println);
```

------

### 중간 연산

생성된 스트림을 필터링하거나 원하는 형태에 알맞게 가공하는 연산이다.

중간 연산의 특징은 반환 값으로 다른 **스트림을 반환**하기 때문에 이어서 호출하는 **메서드 체이닝**이 가능하다.

1. 필터링 : filter(), distinct()
2. 변환 :  map(),flatMap()
3. 제한 : limit(), skip()
4. 정렬 : sorted()
5. 연산 결과 확인 : peek()

```java
.distinct() //중복제거
.filter(Predicate<T> predicate) //조건에 안 맞는 요소는 제외
.limit(long maxSize) //maxSize 이후의 요소는 잘래냄
.skip(long n) //앞에서부터 n개 건너뛰기
.sorted() //기본 정렬로 정렬
.sorted(Comparator<T> comparator) //조건에 맞게 요소 정렬. 추가 정렬 기준을 제공할 때는 thenComparing()사용
    
 //스트림의 요소를 변환. ex) map(File::getName), map(s->s.subString(3))
.map(Function<T> mapper) 
    
 //요소에 작업수행. 보통 중간 작업결과 확인으로 사용. peek(s->System.out.println(s))
.peek(Consumer<T> action)
    
 //스트림의 스트림을 스트림으로 변환
 //ex) Stream<String> strStrm=strArrStrm.flatMap(Arrays::stream)
.flatMap()
```

<aside> 💡 **Predicate란?** argument를 받아 boolean 값을 반환하는 함수형 인터페이스

```java
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
} //T를 인자로 받고 Boolean형을 리턴
```

**함수형 인터페이스란?** 추상 메소드가 오직 하나인 인터페이스(디폴트 메소드는 갯수 상관 없음) 자바의 람다 표현식은 함수형 인터페이스로만 사용 가능하다.

</aside>

------

### 최종 연산

최종 연산은 앞서 중개 연산을 통해 만들어진 Stream에 있는 요소들에 대해 마지막으로 각 요소를 소모하며 최종 결과를 표시한다. 즉, 지연(lazy)되었던 모든 중개 연산들이 최종 연산 시에 모두 수행되는 것이다. **최종 연산 시에 모든 요소를 소모한 해당 Stream은 더 이상 사용할 수 없다.**

1. 요소의 출력 : forEach()
2. 요소의 소모 : reduce()
3. 요소의 검색 : findFirst(), findAny()
4. 요소의 검사 : anyMatch(), allMatch(), noneMatch()
5. 요소의 통계 : count(), min(), max()
6. 요소의 연산 : sum(), averege()
7. 요소의 수집 : collect()

```java
void forEach(Consumer<? super T> action) //각 요소에 지정된 작업 수행
void forEachOrdered(Consumer<? super T> action) //병렬 스트림의 경우 순서를 유지하며 수행
long count() //스트림의 요소 개수 반환
    
Optional<T> max(Comparator<? super T> comparator) //스트림의 최대값 반환
Optional<T> min(Comparator<? super T> comparator) //스트림의 최소값 반환
    
Optional<T> findAny() //아무거나 하나 반환. 벙렬 스트림에 사용
Optional<T> findFirst() //첫 번째 요소 반환. 순차 스트림에 사용
    
boolean allMatch(Predicate<T> p) //모든 조건을 만족?
boolean anyMatch(Predicate<T> p) //조건을 하나라도 만족?
boolean noneMatch(Predicate<T> p) //모든 조건을 만족하지 않음?
    
Object[] toArray() //모든 요소를 배열로 반환
A[] toArray(IntFunction<A[]> generator) //특정 타입의 배열로 반환
    
//스트림의 요소를 하나씩 줄여가면서 계산
//아래에서 자세히 보자
Optional<T> reduce(BinaryOperator<T> accumulator) 

//데이터를 변형 등의 처리를 하고 원하는 자료형으로 변환해 줍니다.
//아래에서 자세히 보자.
collect( ~ )
```