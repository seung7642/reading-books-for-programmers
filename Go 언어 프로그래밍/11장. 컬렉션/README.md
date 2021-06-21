# 컬렉션
## 1. 배열

## 2. 슬라이스 (Slice, 동적 배열)
###### 2.1 슬라이스
슬라이스는 동적 배열이다. (그냥 동적 배열이라 하지 왜 새로운 용어를 만드는지..😑)
즉, Size에 맞춰 자동적으로 Capacity를 늘려준다.

Go Slice 선언은 배열과 동일한데 다만, 첨자를 빈칸으로 둔다.
```go
package main

import "fmt"

func main() {
    var a []int // 슬라이스 변수 선언
    a = []int{1, 2, 3} // 슬라이스에 리터럴값 지정
    fmt.Println(a, len(a), cap(a)) // 값, Size, Capacity 확인
}
```

<br/>

슬라이스에 Size와 Capacity를 지정하지 않으면 디폴트로 둘다 0으로 할당한다.
이를 *Nil Slice*라 하고, nul과 비교하면 참을 반환한다.
> *nil*은 정의되지 않은 메모리 번지다. 
>내부적으로 0x0000000000(주소 40bit)을 가리킨다.
```go
func main() {
    var a []int

    if a == nil {
        println("Nil Slice")
        println(len(a), cap(a)) // Size와 Capacity 확인 (둘다 0 출력)
    }
}
```

<br />

###### 2.2 부분 슬라이스 (Sub-slice)
[1, 2, 3, 4, 5] 슬라이스가 있고, 인덱스 1번부터 3번까지 추출(부분 슬라이스)하고 싶을 때도 있다.
첨자 안을 보면 [1, 4)로 기술되어 있는데, 1이상 4미만이라는 의미다.
```go
func main() {
    a := []int{1, 2, 3, 4, 5}
    subA := a[1:4]
    fmt.Println(subA) // [2, 3, 4] 출력
}
```

<br />

부분 슬라이스 [시작:끝]에서 시작과 끝은 생략할 수도 있다.
시작 인덱스가 생략되면 0부터, 끝 인덱스가 생략되면 마지막 인덱스가 할당된다.
```go
func main() {
    a := []int{1, 2, 3, 4, 5}
    subA := a[:4] // [1, 2, 3, 4]
    subA = a[3:]  // [4, 5]
    subA = a[:]   // [1, 2, 3, 4, 5]
}
```

<br />

###### 2.3 슬라이스 추가와 병합(append), 복사(copy)
Go 내장함수인 append()로 슬라이스의 요소를 추가할 수 있다. 요소를 추가할때 capacity를 초과하게 되면 내부적으로 기존의 2배의 capacity를 가지는 배열을 새로 생성해 기존의 값들을 복사한다.
```go
func main() {
    arr := []int{1, 2, 3}
    println(cap[arr]) // capacity: 3
    
    arr = append(arr, 4, 5) // [1, 2, 3, 4, 5]
    println(cap[arr]) // capacity: 6
}
```

<br />

한 슬라이스를 다른 슬라이스 뒤에 병합할 때도 역시 append() 내장함수를 사용한다. 이때, 특이한 점은 두 번째 파라미터 슬라이스 끝에 ellipsis(...)를 붙여준다는 것. 이걸 붙여주면 해당 슬라이스 요소들의 나열으로 치환된다.
```go
import "fmt"

func main() {
    sliceA := []int{1, 2, 3}
    sliceB := []int{4, 5, 6}
    sliceA = append(sliceA, sliceB...) // sliceB...는 4, 5, 6으로 치환된다.
    fmt.Println(sliceA) // 1, 2, 3, 4, 5, 6
}
```

## 맵 (Map)
