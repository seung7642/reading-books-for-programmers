# 구조체
## 1. 구조체
struct는 Custom Data Type을 표현하는데 사용되고, 필드 데이터만을 가진다.

Go 언어는 전통적인 OOP 언어가 가지는 클래스, 객체, 상속 개념이 없다. 기존의 클래스가 필드와 메서드를 같이 표현했다면, Go는 이를 struct와 메서드로 분리했다.

## 2. Struct 선언
struct 명, 필드명이 소문자로 시작하면 non-public(private), 대문자로 시작하면 public이다.
```go
package main

type person struct {
    name string
    age int
}
func main() {
    p := person{}
    p.name = "Lee"
    p.age = 10
}
```

<br/>

## 3. Struct 생성
Struct 객체의 생성/초기화 방법은 여러가지가 있다.
```go
var p1 person
p1 = person{"Bob", 20}

p2 := person{name: "Alice", age: 15}

p3 := new(person) // 각 필드는 디폴드값으로 세팅
```

<br/>

## 4. 생성자(Constructor) 함수
객체의 생성과 동시에 초기화가 필요할 수도 있다.
```go
type dict struct {
    data map[int]string
}

// 생성자 함수 정의
func newDict() *dict { 
    d := dict{}
    d.data = map[int]string{} // 객체 생성
    return &d
}

func main() {
    dic := newDict()
    dic.data[123] = "Hello, World"
}
```
