# 클래스 
## 클래스와 프로퍼티
```java
public class Person {
	private final String name;
	private int age;

	public Person(String name, int age){
		this.name = name;
		this.age = age;
	}

	public String getName(){
		return name;
	}

	public int getAge(){
		return age;
	}

	public void setAge(int age){
		this.age = age;
	}
}
```
전형적인 자바 Class

```kotlin
class Person constructor(name: String, age: Int) {
	val name: String = name
	var age: Int = age
}
```
- 코틀린에서는 default가 public
- constructor를 최상단에 써준다.
- 필드만 만들면 getter와 setter를 자동으로 만들어준다.

```kotlin
class Person(name: String, age: Int) {
	val name: String = name
	var age: Int = age
}
```
- constructor 지시어는 생략 가능하다

```kotlin
class Person(
	val name: String,
	var age: Int
)
```
- constructor 내에서 프로퍼티 선언까지 가능하다
- 빈 Body는 생략 가능하다
- Class 선언부에 있는 이 생성자가 **Primary Constructor** 인데, 만약 **Primary Constructor에 파라미터가 없는 경우에는 생략 가능**하다.

```kotlin
val person = Person("danny", 29)
println(person.name)
person.age = 10
```
- `.필드` 로 바로 접근하면 getter와 setter가 호출된다.

## 생성자와 init

### init 블록
Person 생성 시점에 나이를 검증해보자

```java
public Person(String name, int age){
	if(age <= 0)
		throw new IllegalArgumentException();

	this.name = name;
	this.age = age;
}
```

코틀린의 생성자는 클래스 선언부에 있는데 위 로직을 어디에 넣을까 ? 
```kotlin
class Person(
	val name: String,
	var age: Int
){
	init{
		if (age <= 0)
			throw IllegalArgumentException()
	}
}
```
> init 블록 : 생성자 호출 시점에 최초 1회 호출되는 블록

인스턴스가 생성될때 Validation 등이 필요하면 **init** 블록 내에서 Validation 가능하다.

### Secondary Constructor
생성자가 여러개 있을때는 어떻게 해야할까 ?
```kotlin
class Person(
	val name: String,
	var age: Int
){
	init{
		if (age <= 0)
			throw IllegalArgumentException()
	}

	constructor(name: String): this(name, 1)

	constructor(): this("danny") {
		println("파라미터 없는 생성자가 호출되었을 때!")
	}
}
```
생성자를 추가하고 싶을 때는 **constructor** 블록을 통해 추가할 수 있다.
- 이 constructor는 반드시 Primary Constructor를 호출해야 한다.
- constructor도 Body를 가져서 호출될 때 로직을 추가할 수 있다.

### 호출 순서
호출 순서는 어떻게 될까 ? 
```kotlin
class Student(  
    val name: String,  
    var age: Int  
) {  
    init {  
        println("Init 블록 호출")  
    }  
  
    constructor(name: String) : this(name, 1) {  
        println("첫번째 Secondary Constructor 호출")  
    }  
  
    constructor() : this("danny") {  
        println("두번째 Secondary Constructor 호출")  
    }  
}
```

위 클래스를 Decompile 해보자
```java
// Decompiled Java
public final class Student {  
   @NotNull  
   private final String name;  
   private int age;  
  
   @NotNull  
   public final String getName() {  
      return this.name;  
   }  
  
   public final int getAge() {  
      return this.age;  
   }  
  
   public final void setAge(int var1) {  
      this.age = var1;  
   }  
  
   public Student(@NotNull String name, int age) {  
      Intrinsics.checkNotNullParameter(name, "name");  
      super();  
      this.name = name;  
      this.age = age;  
      String var3 = "Init 블록 호출";  
      System.out.println(var3);  
   }  
  
   public Student(@NotNull String name) {  
      Intrinsics.checkNotNullParameter(name, "name");  
      this(name, 1);  
      String var2 = "첫번째 Secondary Constructor 호출";  
      System.out.println(var2);  
   }  
  
   public Student() {  
      this("danny");  
      String var1 = "두번째 Secondary Constructor 호출";  
      System.out.println(var1);  
   }  
}
```
- Init 블록은 Primary Constructor로 값을 할당 이후에 블록이 실행된다.
- Secondary Constructor의 블록들은 상위 Constructor가 호출 되고 나서 실행된다.

### Tip
- 왠만하면 Secondary Constructor로 생성자를 여러개 쓰지말고 default parameter를 사용하자.
- 어쩔수 없이 다른 객체를 받아 생성자에서 Converting 해야 하는 경우는 정적 패토리 메소드를 사용하자.
	> 정적 팩토리 메소드 (Static Factory Method)
	> - `new` 키워드를 이용하여 객체를 생성하지 않고 클래스에 선언된 Static 메소드를 호출하여 객체를 반환받는 디자인 패턴
	> - 객체 생성 로직을 캡슐화 함
	> - `new` 가 아닌 명확한 메소드 이름을 통해 명확하게 객체를 설명할 수 있어서 좋음
	> - 싱글톤 패턴처럼 불변 객체와 같은 기존 객체를 반환하여 재사용할 수 있음

```kotlin
class Alien(val name: String)

class Student(val name: String, var age: Int) {
    init {
        println("Init 블록 호출")
    }

    constructor(name: String) : this(name, 1) {
        println("첫번째 Secondary Constructor 호출")
    }

    constructor() : this("danny") {
        println("두번째 Secondary Constructor 호출")
    }

    companion object {
        fun fromAlien(alien: Alien): Student {
            return Student(alien.name)
            // 여기서 추가적인 로직을 통해 Alien 객체로부터 Student 객체를 생성할 수 있습니다.
            // 예를 들어, 이름은 Alien에서 가져오고 나이는 기본 값이나 변환 로직을 통해 설정할 수 있습니다.
        }
    }
}

// 사용 예
val alienInstance = Alien("Zorg")
val student = Student.fromAlien(alienInstance)
println("생성된 학생의 이름: ${student.name}")

```
- 코틀린에서는 `companion object` 를 이용하여 정적 팩토리 메소드를 구현할 수 있다.
	- companion object란 ? [[Companion_Object]]

## 커스텀 getter, setter

```kotlin
class Person(
	val name: String,
	var age: Int
){
	init{
		if (age <= 0)
			throw IllegalArgumentException()
	}

	val isAdult: Boolean
		get() = this.age >= 20 // custom getter
}
```
커스텀 getter를 사용하면 위와 같이 isAdult()를 함수로 만들지 않고 프로퍼티처럼 사용 가능하다.

```kotlin
class Person(
	name: String,
	var age: Int
){
	var name = name
		set(value) {
			field = value.uppercase()
		}
}
```
이렇게 setter도 customize가능 하다. (field는 자기 자신 필드)
사실 custom setter는 많이 사용하지는 않고 따로 함수로 뺌
### backing field
```kotlin
```kotlin
class Person(
	name: String,
	var age: Int
){
	val name = name
		get() = field.uppercase()
}
```
- 위와 같이 기본 getter를 사용하기 위해 Primary Constructor에서 분리하여 따로 custom getter를 선언할 수 있다.
- field.uppercase()를 사용하는 이유
	- name.uppercase()로 호출하면 또 name에 대한 getter가 호출되기 때문에 무한 루프가 발생한다.
	- 따라서, 자기 자신을 가리키는 `field` 라는 예약어를 사용한다.
	- 이 `field`가 **backing field**이다.

보통 custom getter에서 backing field를 사용하지는 않는다.
보통은 이렇게 표현
```kotlin
class Person(
	val name: String,
	var age: Int
){
	val uppercaseName: String
		get() = this.name.uppercase()
}
```
