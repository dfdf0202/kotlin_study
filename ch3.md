### 메서드 정의와 호출
 ```kotlin
 fun main() { 
    val list = listOf(1, 2, 3)
    println(list) // [1, 2, 3]
 }
 ```
list 요소 사이를 세미콜론으로 구분하고 괄호로 둘러 싸고자 한다면 자바에선 직접 구현 해야겠지만  
코틀린에서는 함수가 이미 라이브러리에 있다.  
list 의 요소를 구분자, 접두사, 접미사를 붙여주는 함수를 직접 구현
```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String,
    prefix: String,
    postFix: String
) : String {
    val result = StringBuilder(prefix)
    
    for((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    
    result.append(postFix)
    return result.toString()
}

fun main() {
    val list = listOf(1, 2, 3)
    println(joinToString(list, "; ", "(",")")) // (1; 2; 3)
}
```
파라미터에 디폴트 값을 추가해 파라미터로 넘기는 인자를 줄여보자
```kotlin
fun <T> joinToString(
    collection: Collection<T>,
    separator: String = ", ",
    prefix: String = "",
    postFix: String = ""
) : String {
    val result = StringBuilder(prefix)
    
    for((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }
    
    result.append(postFix)
    return result.toString()
}
fun main() {
    val list = listOf(1, 2, 3)
    println(joinToString(list, ", ", "","")) // 1, 2, 3
    println(joinToString(list))  // 1, 2, 3
    println(joinToString(list, "; "))  // 1; 2; 3
}
```
위 같이 파라미터에 기본값을 넣어주면 오버로딩 효과도 낼 수 있음
```kotlin
fun <T> Collection<T>.joinToString(
    separator: String = ", ",
    prefix: String = "",
    postFix: String = ""
) : String {
    val result = StringBuilder(prefix)

    for((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postFix)
    return result.toString()
}
fun main() {
    val list = listOf(1, 2, 3)
    println(list.joinToString(", ", "","")) // 1, 2, 3
    println(list.joinToString("; ")) // 1; 2; 3

}
```
위와 같이 확장 함수로 만들어 마치 Collection 에 joinToString 이라는 함수를 만든것 같이 사용할 수 있게 된다  
하지만 joinToString 은 Collection 클래스의 일부가 아니며 그로 인해 오버라이딩 할 수 없다

```kotlin
open class View {
    open fun click() = println("View clicked")
}
class Button : View() {
    override fun click() = println("Button clicked")
}

fun View.ShowOff() = println("I`m a view")  // View 확장 함수
fun Button.showOff() = println("I`m a button")  // Button 확장 함수

fun main() {
    val button: View = Button()
    button.showOff() // I`m a view
}

```
위와 같이 Button 에 선언된 확장 함수 showOff 를 실행 해도 View 타입에 해당하는 showOff 확장 함수가 호출 된다.  
이건 컴파일 될 떄 확장 함수를 정적 바인딩을 하는 것이고, 이것 때문에 오버라이딩을 할 수 없는 것  

