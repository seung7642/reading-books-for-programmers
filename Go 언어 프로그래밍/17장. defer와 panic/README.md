# defer와 panic
## 1. 지연실행 defer
Go 언어의 defer 키워드는 특정 문장 혹은 함수를 나중에 (defer를 호출하는 함수가 리턴하기 직전에) 실행하게 한다. 일반적으로 defer는 C#, Java의 finally 블록처럼 마지막의 Clean-up 작업(파일이나 DB커넥션 close와 같은)을 위해 사용된다.
```go
package main

import "os"

func main() {
    f, err := os.Open("/Users/guest/1.txt")
    if err != nil {
        panic(err)
    }

    defer f.Close() // main 마지막에 파일 close 실행 

    // 파일 읽기 
    bytes := make([]byte, 1024)
    f.Read(bytes)

    content := string(bytes[:])
    println(content)
}
```

<br/>

## 2. panic 함수
Go 내장함수인 panic() 함수는 현재 함수를 즉시 멈추고 현재 함수의 defer 함수들을 모두 실행한 후 즉시 리턴한다. 이러한 panic() 모드 실행 방식은 다시 상위함수에도 똑같이 적용되고, 계속 call stack을 타고 올라가며 적용된다. 그리고 마지막에는 프로그램이 에러를 내고 종료한다.
```go
func main() {
    openFile("Invalid.txt") // 잘못된 파일명을 넣음

    println("Done") // 위에서 panic이 실행되면 해당 문장은 실행 안됨 
}

func openFile(fn string) {
    f, err := os.Open(fn)
    if err != nil {
        panic(err)
    }
    defer f.Close()
}
```

<br/>

## 3. recover 함수 
Go 내장함수인 recover() 함수는 panic() 함수에 의한 패닉상태를 다시 정상 상태로 되돌리는 함수이다.
```go
func main() {
    openFile("Invalid.txt")

    println("Done")
}

func openFile(fn string) {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("OPEN ERROR", r)
        }
    }()

    f, err := os.Open(fn)
    if err != nil {
        panic(err)
    }
    defer f.Close()
}
```
