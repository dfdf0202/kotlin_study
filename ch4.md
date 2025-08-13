### 클래스, 객체, 인터페이스
코틀린에서는 상속이나 구성을 클래스 이름 뒤에 콜론(:) 를 붙이고  
인터페이스나 클래스의 이름을 적는 방식  
상위 인터페이스에 있는 프로퍼티나 메서드를 구현할 때는  
자바의 @Override 와 다르게 override 변경자 사용
```kotlin
/**
 * click 이라는 추상 메서드를 가지고 있는 
 * 인터페이스를 정의하는 방법과
 * 이를 구현하는 Button 클래스 정의
 */
interface Clickable {
    fun click()
}

class Button : Clickable {
    override fun click() = println("Clicked")
}

fun main() {
    Button().click() // Clicked
}
``` 
코틀린의 클래스와 메서드는 기본적으로 final    
따라서 클래스의 상속과 오버라이드를 허용하고 싶다면 앞에 open 을 붙어야 함

```kotlin
interface Clickable {
    fun click()
}

open class RichButton : Clickable {
    fun disable() = println("disabled")
    open fun animate() = println("animated")
    override fun click() = println("RichButton clicked")
}

class ThemeButton : RichButton() {
    override fun disable() = println("ThemeButton disabled")
    // 'disable' in 'RichButton' is final and cannot be overridden.
    // 컴파일시 에러 발생
    override fun animate() = println("ThemeButton animated")
    override fun click() = println("ThemeButton clicked")
}

```
abstract 클래스는 인스턴스화 할 수 없고 하위 클래스에서 오버라이드 해야만 하기 때문에  
추상 멤버 앞에 open 명시할 필요가 없음. 인터페이스의 멤버에 open을 명시 하지 않아도  
되는 것과 마찬가지

```kotlin
abstract class Animated {
    abstract val animationSpeed: Double	
    val keyframes : Int = 20	 
    open val frames : Int = 60	 

    abstract fun animate()	
    open fun stopAnimating() = println("stoped") // open 으로 열려 있기 때문에 override 가능
    fun animateTwice() = println("twice")
}

class Kpop : Animated() {
    override val animationSpeed: Double = 1.5 // 반드시 구현 
    // override val keyframes : Int = 30
    // override 시 'keyframes' in 'Animated' is final and cannot be overridden.
    override val frames : Int = 30 // open 으로 열려 있기 때문에 override 가능
    override fun animate() = println("animated") // 반드시 구현
    override fun animateTwice() = println("hi") 
    // 'animateTwice' in 'Animated' is final and cannot be overridden.
}
```
2장에서 클래스 선언 하는 방법을 봤다.
```kotlin
class Person(val name: String)
```
이렇게 중괄호 없이 클래스 이름 뒤에 오는 괄호에 있는 코드를  
<b> 주 생성자 </b> 라고 부름.  
주 생성자는 파라미터를 지정하고, 파라미터에 의해 초기화 됨

```kotlin
class Person constructor(_name: String) {
    val name: String
    init {
        name = _name
    }
}
```
constructor : 주 생성자나 부 생성자 정의를 시작할 때 사용 (생략 가능)
init : 초기화 블럭 
파라미터에 언더스코어(_)는 파라미터와 멤버와 구분할 수 있게 해줌.  
없으면 초기화 블럭에서 this 로 구분 해줘야 함  
함수 파라미터와 마찬가지로 생성자 파라미터에도 기본값을 정의할 수 있음

```kotlin
open class Downloader {
    constructor(url: String?) {
        // TODO
    }
    constructor(uri: URI?) {
        // TODO
    }
}
class MyDownloader : Downloader {
    constructor(url : String?) : super(url) {
        // TODO
    }
    constructor(uri : URI?) : super(uri) {
        // TODO
    }
}

/**
 * 각 생성자가 직접 부모(Downloader)의 해당 생성자를 호출.
 * url 생성자는 Downloader(String?) 호출
 * uri 생성자는 Downloader(URI?) 호출
 * 두 생성자 내부 코드는 서로 완전히 독립적.
 */ 

class MyDownloader : Downloader {
    constructor(url : String?) : this(URI(url))
    constructor(uri : URI?) : super(uri)
}
/**
 * 	url 생성자는 같은 클래스의 uri 생성자를 호출 (this(URI(url))).
 * 	그 결과, url → uri 생성자 → 부모 Downloader(URI?) 호출 순으로 흐름이 이어짐.
 * 	공통 로직을 uri 생성자에만 두고, url 생성자는 변환만 담당.
 */
```

```kotlin
interface User {
    val nickname: String
}

/**
 * 추상 프로퍼티가 있는 인터페이스를 구현할 땐
 * 구현하는 클래스가 nickname 의 값을 얻을 수 있는 방법을 제공 해야 함
 */
class PrivateUser(override val nickname: String) : User
// 주 생성자에 선언 방법을 구현한 예

class SubscribingUser(val email: String) : User {
    override val nickname: String
        get() = email.substringBefore('@')
}
// 커스텀 게터

class SocialUser(val accountId: Int) : User {
    override val nickname = getFacebookName(accountId)
}

fun getFacebookName(accountId: Int) = "kodee$accountId"

fun main() {
    println(PrivateUser("kodee").nickname)
    // kodee
    println(SubscribingUser("test@naver.com").nickname)
    // test
    println(SocialUser(123).nickname)
    // kodee123
}
```
클래스 위임 : by 키워드 사용  
Kotlin에서 인터페이스 구현을 다른 객체에 위임하는 기능  
by 키워드를 사용해서 구현을 직접 쓰지 않고, 이미 구현된 객체에 맡김
```kotlin
interface Printer {
    fun printMessage()
}

class SimplePrinter : Printer {
    override fun printMessage() {
        println("Hello from SimplePrinter")
    }
}

// Printer를 구현해야 하지만 직접 구현하지 않고 SimplePrinter에 위임
class AdvancedPrinter(printer: Printer) : Printer by printer

fun main() {
    val sp = SimplePrinter()
    val ap = AdvancedPrinter(sp)

    ap.printMessage() // Hello from SimplePrinter
}
```
이렇게 되면 위임하는 기본 구현으로 따로 오버라이드 할 필요가 없어짐