# 인터페이스 (interface)
## 1. Go 인터페이스
interface는 메서드들의 집합체로, 구현체는 해당 메서드를 모두 구현해야 한다.
interface는 struct와 마찬가지로 type문을 사용하여 정의한다.
```go
type Shape interface {
    area() float64
    perimeter() float64
}
```

<br/>

## 2. 인터페이스 구현
메서드를 정의할 때와 똑같이 해준다. 
```go
// Square 타입 정의
type Square struct {
    width, height float64
}

// Square 타입의 메서드 정의
func (s Square) area() float64 {
    return (s.width * s.height)
}

// Square 타입의 메서드 정의 
func (s Square) perimeter() float64 {
    return 2 *(s.width * s.height)
}
```

<br/>

처음에 위 코드를 보고 "저게 일반 메서드를 정의한건지, 특정 인터페이스를 구현한건지 어떻게 알 수 있지?" 생각이 들었다. 결론은 정의하는 시점에 굳이 알 필요가 없다. 아래의 인터페이스 사용 부분을 보자.

<br/>

## 3. 인터페이스 사용
인터페이스는 함수의 파라미터로 흔히 사용되는데, 이때 파라미터를 넘기는 과정에서 해당 타입이 해당 인터페이스의 메서드를 모두 구현했는지 체크하면 된다.
```go
func main() {
    shape := Shape{10., 20.} // 숫자 뒤에 . 을 붙임으로써 소수형이라는걸 명시
    showArea(shape)
}

func showArea(shape Shape) {
    println(shape.area())
}
```

## 4. 빈 인터페이스 (empty interface)
Go 표준 패키지들의 함수 Prototype을 살펴보면, 다음과 같은 빈 인터페이스가 자주 등장한다.
```go
func Println(a ...interface{}) (n int, err error);
```

<br/>

Go의 빈 인터페이스는 C#, Java의 Object 객체와 같다.
```go
func main() {
    var x interface{} // 어떤 타입이든 담을 수 있다.
    x = 1 
    x = "Bob"
}

func printIt(vv interfacce{}) {
    fmt.Println(v)
}
```

<br/>

## 5. Type Assertion
Go는 타입 단언(Type Assertion)으로 인터페이스 타입의 a 변수가 타입 T에 속하는지 확인할 수 있다.
다음의 코드를 보자. (인터페이스는 위와 동일)
```go
func main() {
    var a Shape = Square{10., 20.} // 변수의 타입은 인터페이스, 할당은 구현체
   
   // // a 변수의 타입은 인터페이스이다. 아래처럼 Type Assertion을 통해,
   // a는 nil이 아니며, Square 구현체를 가리킨다는걸 검증할 수 있다.
    b := a.(Square) 
}
```