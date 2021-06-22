# 메서드
## 1. Go 메서드
타 언어 OOP의 클래스가 필드와 메서드를 함께 갖는 것과 달리 Go는 struct가 필드만을 가지며, 메서드는 별도로 분리되어 정의된다.

메서드 정의 방법은 함수와 거의 동일한데, func 키워드와 함수명 사이에 '함수가 어떤 struct를 위한 메서드인지'를 추가로 표시해준다. 그리고 이를 receiver라고 부른다.
```go
package main

type Rect struct {
    width, height int
}

func (r Rect) area() int {
    return r.width * r.height
}

func main() {
    rect := Rect{10, 20}
    area := rect.area() // 메서드 호출 
}
```

<br/>

receiver의 타입을 위와 같이 value로 넘길 수도 있지만, 포인터를 사용해 reference로 넘길 수도 있다.
```go
func (r *Rect) area2() int {
    r.width++
    return r.width * r.height
}

func main() {
    rect := Rect{10, 20}
    area := rect.area2() // 메서드 호출
}
```