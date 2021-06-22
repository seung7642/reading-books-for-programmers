# 패키지
## 1. Go 패키지
Go는 패키지(Package)를 통해 코드의 모듈화를 제공한다. 우선 모듈과 패키지 용어를 구분해야 한다.
> 모듈은 하나의 워크스페이스(프로젝트)를 의미하고, 패키지는 자기가 속한 디렉토리명이다.

<br />

## 2. main 패키지
main 이라고 명명된 패키지는 main() 함수를 담고 있다. (Entry Point)

<br />

## 3. 사용자 정의 패키지
나만의 라이브러리를 만들어보자.
main 패키지: /goApp/
모듈 패키지: /mylib/

###### 모듈 패키지 작성
1. /mylib/hello.go를 만들고 다음과 같이 작성한다.
```go
package mylib // 소속된 디렉토리명

import "fmt"

func Hello() {
    fmt.Println("Hello")
}
```

<br />

2. 터미널에서 '> go mod init mylibrary' 로 go.mod 파일을 생성한다. (/mylib 경로에서 쳐야됨)
패키지명과 모듈명을 일부러 다르게 줬는데, main에서는 해당 모듈명으로 접근한다.

###### main 패키지 작성
1. /goApp/run.go를 만들고 다음과 같이 작성한다.
```go
package main

import "demo.com/mylibrary"

func main() {
    mylib.Hello()
}
```

<br />

2. 터미널에서 '> go mod init main' 로 go.mod 파일을 생성한다. (/goApp 경로에서 쳐야됨)
3. 생성된 go.mod 파일을 다음과 같이 작성한다.

```
module main

go 1.16

require demo.com/mylibrary v0.0.1

replace demo.com/mylibrary => ../mylib
```

<br />

require로 main에서 필요한 모듈을 컴파일러에게 알려주고, replace로 해당 모듈의 경로를 정의한다.