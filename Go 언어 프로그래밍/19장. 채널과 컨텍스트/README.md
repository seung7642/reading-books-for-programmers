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
채널에서 데이터를 가져오고 싶은데, 채널에 데이터가 없다면 누군가 채널에 데이터를 넣어줄 때까지 무한정 기다려야(blocking) 한다. 이런 상황에서 무한정 대기하는게 아닌, 다른 작업을 하고 있다가 채널에 데이터가 들어오면 그때 해당 작업을 하고 싶거나(non-blocking), 여러 채널에서 동시에 값을 가져오고 싶을 때 select문을 사용한다. 요약하면, 다음 두 가지 상황에서 select문을 사용한다.
1. 채널에서 값을 읽어오는 작업을 *non-blocking*으로 처리하고 싶을 때
2. 하나의 채널이 아닌, 여러 개의 채널에서 *동시에* 값을 읽어오고 싶을 때 (동시에 대기한다는게 포인트)

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
    quit := make(chan bool)

    wg.Add(1)
    go square(&wg, ch, quit)

    for i := 0; i < 10; i++ {
        ch <- i * 2
    }

    quit <- true
    wg.Wait()
}

func square(wg *sync.WaitGroup, ch chan int, quit chan bool) {
    for {
        select { // ch와 quit 채널을 동시에 대기한다. (각각 따로 데이터를 가져오는게 아님)
        case n := <-ch:
            fmt.Printf("Square: %d\n", n * n)
            time.Sleep(time.Second)
        case <-quit: // 채널에서 가져온 데이터를 사용하지 않을거라면 이런 식으로 써준다.
            wg.Done()
            return
        }
    }
}
```

### 1.4 채널로 생산자-소비자 패턴 구현하기
```go
package main
import (
    "fmt"
    "sync"
    "time"
)

type Car struct {
    Body string
    Tire string
    Color string
}

var wg sync.WaitGroup
var startTime = time.Now()

func main() {
    tireChannel := make(chan *Car)
    paintChannel := make(chan *Car)

    fmt.Printf("Start Factory\n")

    wg.Add(3)
    go MakeBody(tireChannel) 
    go InstallTire(tireChannel, paintChannel)
    go PaintCar(paintChannel)

    wg.Wait()
    fmt.Println("Close the factory")
}

func MakeBody(tireChannel chan *Car) {
    tick := time.Tick(time.Second)
    after := time.After(10 * time.Second)
    for {
        select {
        case <-tick:
            tireChannel <- &Car{Body: "Sports car"}
        case <-after:
            close(tireChannel)
            wg.Done()
            return
        }
    }
}

func InstallTire(tireChannel, paintChannel chan *Car) {
    for car := range tireChannel {
        time.Sleep(time.Second)
        car.Tire = "Winter tire"
        paintChannel <- car
    }
    close(paintChannel)
    wg.Done()
}

func PaintCar(paintChannel chan *Car) {
    for car := range paintChannel {
        time.Sleep(time.Second)
        car.Color = "Red"
        duration := time.Now().Sub(startTime) // 프로그램 경과시간을 구한다.
        fmt.Printf("%.2f Complete Car: %s %s %s\n", duration.Seconds(),
                car.Body, car.Tire, car.Color)
    }
    wg.Done()
}
```

## 2. 컨텍스트(context)
컨텍스트는 context 패키지에서 제공하는 기능으로 작업을 지시할 때 작업 가능 시간, 작업 취소 등의 조건을 지시할 수 있는 작업 명세서 역할을 한다. 
```go
package main 
import (
    "fmt"
    "sync"
    "time"
    "context"
)

var wg sync.WaitGroup

func main() {
    wg.Add(1)
    ctx, cancel := context.WithCancel(context.Background())
    go PrintEverySecond(ctx)
    time.Sleep(5 * time.Second)
    cancel()

    wg.Wait()
}

func PrintEverySecond(ctx context.Context) {
    tick := time.Tick(time.Second)
    for {
        select {
        case <-ctx.Done():
            wg.Done()
            return
        case <-tick:
            fmt.Println("Tick")
        }
    }
}
```
