# Select

2개의 채널이 있다고 보자

car Chan ->            |-----------------|

​							    |     factory    |

plane Chan ->       |-----------------|

차가 먼저 공정에 들어가면 비행기는 공정을 기다려야한다.

그래서 `Select`가 있는데 이는 차와 비행기 둘 다 기다리도록 만든다.

Select case 서로 비슷 Switch case = 변수의 값에 따라서 진행되는 분기문

Select case = 기다리고 있는 채널들을 여러 개 걸어두고 그중에 하나가 나올 때 거기에 맞는 함수를 실행 시켜준다.

다음과 같이 사용한다.

```go
//...
func MakeTire(carChan chan Car, planeChan chan Plane, outCarChan chan Car, outPlaneChan chan Plane) {
	for {
		select {
		case car := <-carChan:
			car.val += "Tire_C, "
			outCarChan <- car
		case plane := <-planeChan:
			plane.val += "Tire_P, "
			outPlaneChan <- plane
		}
	}
}
//...
```

go의 타임 패키지에는 시간 간격으로 하는 일을 쉽게하기 위해 `Tick`과 `After`를 제공하고 있다.

`Tick`은 일정 시간을 반복하면서 channel에 알려주고 `After`는 정해진 시간 이후에 channel에 알려준다.

사용 방법은 다음과 같다.

```go
//...
func main() {
	c := make(chan int)

	go push(c)
	
	timerChan := time.After(10*time.Second)
	tickTimerChan := time.Tick(2 * time.Second)

	for {
		select {
		case v := <-c:
			fmt.Println(v)
		case <-timerChan:
			fmt.Println("timeout")
			return
		case <-tickTimerChan:
			fmt.Println("Tick")
		}
	}
 //...
```

