# 에러처리
## 1. Go 에러
Go는 내장 타입으로 모든 error 타입의 조상인 error라는 interface 타입을 갖는다. 해당 인터페이스를 구현하는 커스텀 에러 타입을 정의할 수 있다. error 인터페이스는 하나의 메서드를 가진다.
```go
type error interface {
    Error() string
}
```

<br/>

## 2. Go 에러처리
커스텀 에러 타입을 정의하고 log로 찍어보자.
우선, 아래와 같은 커스텀 에러 타입을 사용자 정의 패키지에 만든다.
```go
package error

type MyError struct {
    Code string
    Message string
}

func (err *MyError) Error() string {
    return err.Code + ", " + err.Message
}
```

<br/>

에러가 발생했을 경우, 위 커스텀 에러 타입의 메시지를 로그에 찍어보자.
```go
package main

import (
    "log"
    "os"
    mylib "demo.com/myModule"
)

func main() {
    f, err := os.Open("~/text")
    if err != nil {
        log.Fatal(mylib.MyError{Code: 400, Message: "bad Message"})
    }
    println(f.Name())
}
```