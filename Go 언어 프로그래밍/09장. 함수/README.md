# 함수
## 1. 일반함수
### 1.1 함수의 기본형
```go
func add(a int, b int) int {
    return a + b
}
```
<br/>

### 1.2 Pass By Reference (call by reference)
Go는 포인터를 가지기 때문에 파라미터로 주소를 넘길 수 있다.
```go
func say(msg *string) {
    println(*msg)
    *msg = "Changed" // 메시지 변경
}
```
<br/>

### 1.3 가변인자
Go는 가변 파라미터를 나타낼 수 있다.
```go
func sum(nums ...int) int {
    var result int = 0
    for _, num := range nums {
        result += num
    }
    return result
}
```
<br/>

### 1.4 복수 개의 리턴 값
Go는 함수의 복수개의 리턴값을 가질 수 있다.
```go 
func main() {
    cnt, res := sum(1, 2, 3, 4, 5)
    println(cnt, res)
}
func sum(nums ...int) (int, int) {
    result := 0
    count := 0
    for _, num := range nums {
        result += num
        count++
    }
    return count, result
}
```
<br/>

## 2. 익명함수 & 일급함수
###### 익명함수
함수명을 갖지 않는 함수를 익명함수라 한다. 익명함수는 다음의 경우에 사용한다.
1. 함수 자체를 변수에 할당할 때
2. 함수의 파라미터로 함수를 넘길 때

<br/>

###### 일급함수
Go 언어에서 함수는 일급함수로서 기본 타입과 동일하게 취급된다.
따라서, 타 함수의 파라미터로 전달하거나, 리턴값으로 사용될 수 있다.
```go
func main() {
    // 변수 add에 익명함수 할당
    add := func(i int, j int) int {
        return i + j
    }

    r1 := calc(add, 10, 20) // add 함수를 파라미터로 전달
    r2 := calc(func(x int, y int) int { // 함수의 파라미터로 익명함수를 전달
        return x - y
    }, 10, 20)
}
func calc(f func(int, int) int, a int, b int) int {
    return f(a, b)
}
```