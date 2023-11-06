
코틀린은 Primitive Type과 Wrapper Type을 따로 구분하지 않는다.

```kotlin
val number: Int = 10
```

자바였으면 int 아니면 Integer가 되었어야 했는데, 코틀린은 그냥 Int 이다.

그럼 코틀린은 비효율적이게 모두 Wrapper Class로 연산하는건가? 싶은데 그렇지 않다.

코틀린은 연산이 필요한 타입들은 Primitive Type으로 컴파일 시 자동으로 변환하여 Wrapper Class의 Heap 차지, boxing unboxing에 대한 오버헤드를 없앤다.

```kotlin
val number1: Int = 1
val number2: Int = 2
println(number1 + number2)
```

위 코드를 Java로 Decompile 하면 아래와 같다.
```java
int number1 = 1;  
int number2 = 2;  
int var2 = number1 + number2;  
System.out.println(var2);
```

Integer가 아닌 int로 바뀌는 것을 볼 수 있다.

그럼 `Int?` 타입은 `Integer` 가 될까 하는 의문이 들었다.
```kotlin
val number1: Int? = 1  
println(number1)
```

```java
Integer number1 = 1;  
System.out.println(number1);
```

역시나 null을 저장할 수 있는 Integer 클래스로 Decompile 된다.

그러면 Collection을 쓸때는 어떻게 될까?
```kotlin
val number1: Int = 1  
val number2: Int = 2  
  
println(number1 + number2)  
  
var list: List<Int> = listOf(number1, number2)
```
```java
int number1 = 1;  
int number2 = 2;  
int var2 = number1 + number2;  
System.out.println(var2);  
List list = CollectionsKt.listOf(new Integer[]{Integer.valueOf(number1), Integer.valueOf(number2)});
```
역시 단순 연산을 위해 primitive type으로 선언되고 Collection에 사용될때만 Boxing되는 것을 볼 수 있다.
