# Thread

### Thread 란?

**Thread**는 일종에 일의 갯수이다. **CPU**는 한번에 하나의 일밖에 할 수 없다. 그래서 옥타 코어 같이 한번에 8가지 일을하는 __CPU__가 좋은 것이다. __CPU__는 하나의 일을 하는 도중에 다른 일을 하고 다시 돌아와서 하던 일을 하는데 그것을 __CONTEXT SWITCHING__이라고 한다.

 하지만 이러한 __CONTEXT SWITCHING__은 비용을 발생시킨다. 그래서 여러개의 프로그램을 실행하게 되면 __CONTEXT SWITCHING__이 발생하게 되는데 이러한 것을 줄이기 위해 __GO__는 __GO THREAD__라는 것을 지원한다.  __OS__단의 __THREAD__를 __KERNEL THREAD__라고 하는데 __GO__는 이를 포장해 와서 __GO THREAD__로 포장한다. 그리고 여러개의 쓰레드를 잘게 잘라서 최대한 __CPU__의 개수에 맞게 쓰레드를 나눈다.

### 프로그램 , 프로세스, Thread의 차이

---

프로그램 >= 프로세스 

​                            ^    여러 개 Thread 소유

​                      Thread

프로그램과 프로세스는 비슷한 개념이고 프로그램이 좀 더 큰 개념이라고 할 수 있다.  프로세스는 여러 개의 Thread로 이루어져 있다.

순서로 따져 본다면 `프로그램 > 프로세스 > Thread` 라고 할 수 있다

#### 프로그램

---

게임, 음악, 그림과 같은 프로그램이 있다고 한다면

이 의미에는 `실행파일 + 데이터`가 포함되어있다.

프로그램은 하나의 실행파일을 가지고 있지만 반드시 하나의 실행파일을 가지고 있지않다. 

프로그램이 실행되는 과정

프로그램이 실행된다는 의미란:

`os`입장에서 우리가 아이콘을 `더블클릭`하면 실행을 하게 되는데 그것의 실행파일 `.exe`파일을 메모리에 `load` 한다.  `os`는 `load`된 장소 즉,  `ip`포인트를 표시해 놓는다. 

프로그램은 논리적인 개념으로 하나의 기능별로 묶여있고 프로세스는 이러한 프로그램이 실행된 형태이고 이 프로세스는 여러 개의 `Thread`로 이루어져 있다.

#### Thread

---

**Thread**는 많은 장점이 있지만 단점 도한 있다.

- 동기화 문제

예) 하나의 그림판 위에서 여러명의 아이들이 그림을 그리게 되면 상대방의 그림을 마구 덮어써서 그림을 못 알아보게 된다.

다음의 코드를 통해 `Thread`가 어떻게 그림을 엉망이로 만들 수 있는지 볼 수 있다.

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

type Account struct {
	balance int
}

func (a *Account) Widthdraw(val int){
	a.balance -= val
}

func (a *Account) Deposit(val int) {
	a.balance += val
}

func (a *Account) Balance() int {
	return a.balance
}

var accounts []*Account

func Transfer(sender, receiver int, money int) {
	accounts[sender].Widthdraw(money)
	accounts[receiver].Deposit(money)
}

func GetTotalBalance() int {
	total := 0
	for i := 0; i < len(accounts); i++ {
		total  += accounts[i].Balance()
	}
	return total
}

func RandomTransfer() {
	var sender, balance int
	for {
		sender = rand.Intn(len(accounts))
		balance = accounts[sender].Balance()
		if balance > 0 {
			break
		}
	}

	var receiver int
	for {
		receiver = rand.Intn(len(accounts))
		if sender != receiver {
			break
		}
	}

	money := rand.Intn(balance)
	Transfer(sender, receiver, money)
}

func GoTransfer() {
	for {
		RandomTransfer()
	}
}

func PrintTotalBalance() {
	fmt.Printf("Total: %d\n", GetTotalBalance())
}

func main() {
	for i := 0; i<20; i++ {
		accounts = append(accounts, &Account{balance:1000})
	}

	PrintTotalBalance()

	for i:= 0; i< 10; i++ {
		go GoTransfer()
	}

	for {
		PrintTotalBalance()
		time.Sleep(100*time.Millisecond)
	}
}
```

위의 코드는 1000원 씩 가지고 있는 20명의 적금자들 끼리 무작위 금액을 서로 송금하는 것이다. 그리고 그 금액의 총액을 `print`한다. 생각을 해보았을 때 이 코드는 20000원을 지속적으로 출력해야하지만 그렇지 않다.

아래는 이 코드로 인한결과 이다.

```cmd
Total: 20000
Total: 20000
Total: 20841
Total: 37135
Total: 11618
Total: 14732
Total: 10431
Total: 24569
Total: 14933
Total: 14388
Total: 7064
Total: 9191
```

go를 통해 10개의 `Thread`로 랜덤 값을 송금하자 20000을 유지해야할 값이 뒤죽 박죽이 되었다. 

반면 하나의 `Thread`로 실행하자  일정한 숫자가 나온다.

```
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
```

`a.balance -= val`을 보자  이 코드는 한 줄로 이루어져 있지만 컴퓨터는 이보다 많은 일을 한다.

-->`a.balance = a.balance - val`우선 이렇게 변화하고 이후

-->`a.balance - val`이 결과 값을 `a.balance`에 대입하게 된다.

즉, 컴퓨터 입장에선 이렇게 된다.

--> Load RegA a.balance

--> Load RegB val

--> Del RegA RegB , &a.balance

이렇게 한번에 계산하지 않고 3번의 `operation`으로 나뉘어서 수행된다.

그래서 한 쪽에서 계산을 하던 와중에 다른 한 쪽에서 계산을 하고 있을 수도 있는 것이다.

| CPU1                        | CPU2                           |
| --------------------------- | ------------------------------ |
| 1:1000     500->     2:1000 | 2:1000     200->    1: 1000    |
| Load RegA 1000              | Load RegA 1000                 |
| Load RegB 500               | Load RegB 200                  |
| Del RegA RegB &a.balance    | Del RegA - RegB   = &b.balance |
| 1: 500 2: 1500              | 2: 800 1: 1200                 |
| 1: 500 2: 800               |                                |

이렇게 작업이 동시에 진행할 때 엉망진창 되어서 마지막 행처럼 값이 섞이게 된 것이다.



#### 문제를 푸는 방법

락(**lock**)을 건다. (대표적인 방법) 대표적으로 **Mutex**라는 것이 있다.





##### Golang에서 Mutex쓰기

```go
//...
type Account struct {
	balance int
	mutex *sync.Mutex
}

func (a *Account) Widthdraw(val int){
	a.mutex.Lock()
	a.balance -= val
	a.mutex.Unlock()
}

func (a *Account) Deposit(val int) {
	a.mutex.Lock()
	a.balance += val
	a.mutex.Unlock()
}

func (a *Account) Balance() int {
	a.mutex.Lock()
	balance := a.balance
	a.mutex.Unlock()
	return balance
}
//...
```

이와 같이 보호해줄 값에 락(**lock**)을 걸어 두었다. 

이후 실행하자 아까보다는 비교적 일관된 값을 출력한다.

```
Total: 20000
Total: 18756
Total: 21897
Total: 20865
Total: 19996
Total: 21316
Total: 20032
Total: 20000
Total: 19646
Total: 20000
Total: 20000
Total: 20000
```

아직 문제가 있는데 그 이유는 무엇일까?

```go
//...
func Transfer(sender, receiver int, money int) {
	accounts[sender].Widthdraw(money)
	accounts[receiver].Deposit(money)
}

func GetTotalBalance() int {
	total := 0
	for i := 0; i < len(accounts); i++ {
		total  += accounts[i].Balance()
	}
	return total
}
//...

```

송금으로 빼고 난 후에 전체 값이 출력되면 더 적게 나오는 것이고 입금 후 출력을 하게 되면 더 많이 나오게 되는 것이다. 그러므로 위 두 함수에도  **lock**을 써야한다.

```go
//...
var globalLock *sync.Mutex

func Transfer(sender, receiver int, money int) {
	globalLock.Lock()
	accounts[sender].Widthdraw(money)
	accounts[receiver].Deposit(money)
	globalLock.Unlock()
}

func GetTotalBalance() int {
	globalLock.Lock()
	total := 0
	for i := 0; i < len(accounts); i++ {
		total  += accounts[i].Balance()
	}
	globalLock.Unlock()
	return total
}
//...
func main() {
	for i := 0; i<20; i++ {
		accounts = append(accounts, &Account{balance:1000, mutex: &sync.Mutex{}})
	}
	globalLock = &sync.Mutex{}

//...
```

위와 같이 `globalLock.Lock`을 해주게 되면 아래와 같이 값이 일정하게 줄력된다.

```
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
Total: 20000
```

그래서 Locking을 어느정도 범위로 하는지 굉장히 어렵다. 그리고 Locking에는 데드 락이라는 문제 또한 발생한다.그래서 `Golang`은 `channel`을 제공한다. 하지만 `channel`이 모든 것을 해결해 주지 않는다.

##### Dead Lock이란?

---

락이 죽었다고 직해할 수 있지만 이는 막다른 길(**Dead end**와 같은 의미로 해석할 수 있다.

```go
//...
func Transfer(sender, receiver int, money int) {
	accounts[sender].mutex.Lock()
	fmt.Println("Lock", sender)
	time.Sleep(1000 * time.Millisecond)
	accounts[receiver].mutex.Lock()
	fmt.Println("Lock", receiver)
	
	accounts[sender].Widthdraw(money)
	accounts[receiver].Deposit(money)
	
	accounts[receiver].mutex.Unlock()
	accounts[sender].mutex.Unlock()
	fmt.Println("Transfer", sender, receiver, money)
}
//...
```

모든 락을 지우고 이렇게 락을 설정해 보았다.

그러자

```
Lock 1
Lock 0
```

여기서 멈추게 된다.

이것의 대표적인 예는 **철학자의 식사시간**이다.

> 여러 명의 철학자들이 둥근 테이블에 둘러 앉아 음식을 먹으려고 한다. 이 철학자들은 포크를 양손에 들어야만 밥을 먹을 수 있다. 그런데 이 철학자들이 항상 왼쪽 손이 먼저 포크를 들고 그 후 오른쪽 손으로 포크를 들고 밥을 먹는다고 해보자 동시에 왼쪽의 포크를 들고 오른쪽의 포크를 들려고 하자 오른쪽 포크는 다른 철학자가 들고 있다. 결국 철학자들은 서로가 든 포크때문에 밥을 못먹게 된다.

이를 상태를 **Dead Lock**이라고 한다.

이 **Dead Lock**을 피하려면 **Lock**을 작게 잡거나 크게 잡아야한다. (처음에 했던 것과 같이)