### 함수
- 파라미터와 반환값이 없는 함수 선언법
```kotlin
/**
 * 세미콜론(;) 생략 가능. 꼭 필요한 경우가 아니면 붙이지 않는 것을 권장 
 */
fun main() {
    println("hello world")
}
```
- 파라미터와 반환값이 있는 함수 선언법
```kotlin
/**
 * fun : 함수 선언
 * max : 함수 명
 * a, b : 파라미터
 * : Int : 반환 값
 */
fun max(a: Int, b: Int) : Int { 
    return if (a > b) a else b  // 함수 본문, return a > b ? a : b; 와 같은 의미
}
```
```kotlin
/**
 * 위 함수를 간단히 처리 할 수 있음
 * 중괄호를 제거 한 후 등호(=) 뒤에 위치 시켜야 함
 * 위 함수 같이 중괄호로 쌓인 함수를 블록 본문 함수, 등호와 식으로 이루어진 함수를 본문 함수 라고 함
 */
fun max(a: Int, b: Int): Int = if(a > b) a else b
// 함수인데 반환 타입이 없는건 컴파일러가 컴파일 시점에 함수 본문 식을 분석해 결과 타입을 반환 타입으로 정해줌
// 이런 분석을 타입 추론 이라고 함
```

### 변수
코틀린 변수는 var, val 로 선언

- var : 선언된 변수의 값을 변경할 수 있는 선언
- val : 값을 바꿀 수 없는 선언 (읽기 전용이며 java의 final)

```kotlin
val keyword: String = "hello world"
val year: Int = 2025
```
타입 추론으로 타입 선언을 생략해 간결하게 만들 수 있음
```kotlin
val keyword = "hello world"
val year = 2025
```
하지만 변수를 선언하고 초기화 하지 않고 나중에 값을 대입하고 싶을 때는 명시적으로 타입을 지정 해야 함.
```kotlin
fun main() {
    //val year
    //year = 2025 // X
    var year: Int
    year = 2025
}
```

### 클래스
```java
/**
 * Java 에서 클래스
 */
public class Person {
    private final String name;
    private Boolean isStudent;

    public Person(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public Boolean getIsStudent() {
        return isStudent;
    }
    public void setIsStudent(Boolean isStudent) {
        this.isStudent = isStudent;
    }
}
```
```kotlin
/**
 * Kotlin 에서 클래스
 */
class Person(val name: String, var isStudent: Boolean)

fun main() {
    val a = Person("Joy", true)
    println(a.name) // 'Joy'
    println(a.isStudent) // true
    
    a.isStudent = false
    println(a.isStudent) // false
    a.name = 'Bob' // error 발생. val 은 읽기 전용
}
```
```kotlin
/**
 * 커스텀 getter
 */
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Booelan
        get() {
            return height == width
        }
}

fun main() {
    val r = Rectangle(45, 45)
    println(r.isSquare) // true
}
```

### 이넘 (Enum)
```kotlin
/**
 * 기본 enum 클래스 
 */
enum class Color {
    RED, ORANGE, YELLOW, GREEN
}
```
```kotlin
/**
 * 프로퍼티가 있는 enum 클래스
 */
enum class Color(
    val r: Int,
    val g: Int,
    val b: Int
) {
    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255);   // 메서드를 정의하는 경우 상수와 메서드 사이에 세미콜론을 넣어야 함
    
    fun rgb() = (r * 256 + g) * 256 + b
    fun printColor() = println("$this is ${rgb()}")
}

fun main() {
    println(Color.GREEN.rgb()) // 65280
    Color.GREEN.printColor()   // GREEN is 65280
}
```

### When 
when 은 java 의 switch 문
```kotlin
fun getMnemonic(color: Color) = 
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
    }

fun getWarmFromSensor(color: Color): String {
    return when(color) {
        Color.RED, Color.ORANGE, Color.YELLOW -> "warm ($color = ${color.r})"
        Color.GREEN -> "natural ($color = ${color.g}"
        Color.BLUE -> "cold ($color = ${color.b})"
    }
}

fun main() {
    println(getMnemonic(Color.GREEN)) // Gave
    println(getWarmFromSensor(Color.BLUE)) // cold (BLUE = 255)
}
```

### for

```kotlin
/**
 * 코틀린은 (int i = 0; i < 10; i++) 같은 고전적인 루프를 사용하지 않음
 * (1..10) 이런식으로 일정한 순서로 루프를 사용함
 */
fun main() {
    for (i in 1..10) {
        printl(i) // 1, 2, 3, ... 10
    }
}
```
```kotlin
/**
 * 역방향과 조건 추가
 * ex) 10 부터 짝수만 찍는 코드
 */
fun main() {
    for (i in 10 downTo 1 step 2) {
        println(i) // 10, 8, 6, ... 2
    }
}
```
```kotlin
/**
 * List 
 */
fun main() {
    val list = listOf("red", "green", "blue")
    for (color in list) {
        print("$color") // red green blue
    }
}
```

```kotlin
/**
 * Map
 */
fun main() {
    val binaryReps = mutableMapOf<Char, String>()
    for (char in 'A'..'F') {
        val binary = char.code.toString(radix = 2)
        binaryReps[char] = binary
    }

    for ((letter, binary) in binaryReps) {
        // letter 에 key, binary 에 value
        println("$letter = $binary")
        /*
        A = 1000001
        B = 1000010
        C = 1000011
        D = 1000100
        E = 1000101
        F = 1000110
        */
    }
}
```