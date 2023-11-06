# Companion Object란? 

클래스의 인스턴스 없이 companion object내의 필드와 메소드를 바로 접근할 수 있다.

즉, 자바의 Static과 비슷한 기능이다.
```kotlin
class MyClass {
    companion object {
        val companionVar = "I am in companion object"
        fun companionMethod() {
            println("Companion method called")
        }
    }
}

fun main() {
    println(MyClass.companionVar) // 직접 접근
    MyClass.companionMethod() // 직접 접근
}
```

한 클래스는 최대 단 하나의 companion object만 사용 가능하다.

companion object는 내부적으로 Singleton 객체를 통해 동작한다.
```java
public class MyClass {
    private static final MyClass.Companion Companion = new MyClass.Companion();

    public static final class Companion {
        // ... 여기에 companion object의 멤버들이 위치합니다.
    }
}
```
즉, companion object를 선언하면 적어도 하나의 객체가 Heap 메모리에 만들어 지는 것이다.

또한 companion object는 이름이 있을 수 도 있고, 없을 수 도 있다.
```kotlin
// 이름 있는 companion object
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}

fun main() {
    val myClassInstance = MyClass.Factory.create()
}

// 이름은 없어도 된다
class MyClass {
    companion object {
        fun create(): MyClass = MyClass()
    }
}

fun main() {
    val myClassInstance = MyClass.create()
}
```

# Factory Pattern
companion object를 통해 인터페이스를 구현할 수 있다.
특히 팩토리 패턴을 구현할 때 사용하면 좋다.
```kotlin
// 일반적인 인터페이스 정의
interface Sender {
    fun send(): String
}

// 구현 클래스들
class KakaotalkSender : Sender {
    override fun send() = "카카오톡 전송"
}

class SMSSender : Sender {
    override fun send() = "SMS 전송"
}

// 팩토리 인터페이스
interface SenderFactory {
    fun createSender(type: MessageType): Sender
}

// 클래스 내부에 팩토리 인터페이스를 구현하는 companion object 정의
class SenderCreator {
    companion object : SenderFactory {
        override fun createSender(type: MessageType): Sender {
            return when (type) {
                MessageType.Kakaotalk -> KakaotalkSender()
                MessageType.SMS -> SMSSender()
                else -> throw IllegalArgumentException("Unknown message type")
            }
        }
    }
}

class SenderCreator: SenderFactory{
	override fun createSender(type: MessageType): Sender {
	return when (type) {
		MessageType.Kakaotalk -> KakaotalkSender()
		MessageType.SMS -> SMSSender()
		else -> throw IllegalArgumentException("Unknown message type")
		}
	}
}

fun main() {
    val kakaotalkSender = SenderCreator.createSender(MessageType.Kakaotalk)
    println(kakaotalkSender.send())

    val smsSender = SenderCreator.createSender(MessageType.SMS)
    println(smsSender.send())
}

```