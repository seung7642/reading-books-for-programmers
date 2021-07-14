# 채널과 컨텍스트
## 1. 채널 (Channel)
채널은 고루틴끼리 메시지를 전달할 수 있는 메시지 큐이다. <- 연산자를 통해 데이터를 채널에 넣고, 뺄 수 있고, 채널 인스턴스 생성은 슬라이스, 맵과 같이 make() 함수로 한다.
```go
package main
import (
    "fmt"
    "sync"
    "time"
)

func main() {
    var wg sync.WaitGroup
    ch := make(chan int)

    wg.Add(1)
    go square(&wg, ch)
    ch <- 9 // 채널에 데이터 삽입 
    wg.Wait() // 고루틴의 실행이 모두 완료될 때까지 기다린다.
}

func square(wg *sync.WaitGroup, ch chan int) {
    n := <-ch // 채널에서 데이터를 가져온다. (채널에서 가져올 데이터가 없으면 생길 때까지 blocking된다.)
    
    time.Sleep(time.Second)
    fmt.Printf("Square: %d\n", n * n)
    wg.Done()
}
```

### 1.1 버퍼를 가진 채널
내부에 데이터를 보관할 수 있는 메모리 영역을 버퍼(buffer)라고 부른다. 채널의 버퍼용량은 make() 함수의 두 번째 파라미터로 넘겨준다.
```go
var messages chan string = make(chan string, 4)
```

### 1.2 채널에서 데이터 대기 
for range 구문을 사용하면 채널에서 데이터를 계속 기다릴 수 있다. 이때, 루프를 벗어나기 위해선 close() 함수로 채널을 닫아줘야하는데, 채널을 닫아주지 않아 고루틴에서 데이터를 기다리며 무한 대기하는 경우를 *좀비 루틴* 또는 _고루틴 릭_(leak)이라고 한다.
```go
package main
import (
    "fmt"
    "sync"
    "time"
)

func main() {
    var wg sync.WaitGroup
    ch := make(chan int)

    wg.Add(1)
    go square(&wg, ch)

    for i := 0; i < 10; i++ {
        ch <- i * 2
    }
    close(ch) // 더 이상 채널을 사용하지 않는다면 close()해줘야함.
    wg.Wait()
}

func square(wg *sync.WaitGroup, ch chan int) {
    for n := range ch { // 채널에서 데이터를 무한정 기다리다가, close()로 채널이 닫히면 루프를 종료한다.
        fmt.Printf("Square: %d\n", n * n)
        time.Sleep(time.Second)
    }
    wg.Done()
}
```

### 1.3 select문


## 2. 컨텍스트