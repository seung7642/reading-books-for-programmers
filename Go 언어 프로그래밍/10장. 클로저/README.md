# 클로저 (Closure)
Go 언어에서 함수는 Closure로서 사용될 수도 있다. 
> 클로저는 내부함수가 외부함수의 context에 접근할 수 있는 것을 가르킨다. -생활코딩-

<br/>

다음의 코드를 보자.
return문의 내부함수가 자기 외부의 컨텍스트에 있는 변수를 사용하고 있다.
```go
func main() {
    next := func() int {
        i := 0
        return func() int {
            i++
            return i
        }
    }

    println(next()) // 1
    println(next()) // 2
    println(next()) // 3
}
```