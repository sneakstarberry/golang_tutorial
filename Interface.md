# Interface

왔다갔다하는 어떤 면이라고 할 수 있다. 

User Interface 게임과 유저간의 접촉면 즉 관계를 말하는 것이다.

Interface는 객체간 상호 관계를 정의한 것이다.

A와 B 두 객체간의 관계를 정의한 것을 Interface라고 한다.

객체 object

상태 + 기능 - 외부 공개기능

​				    -  내부 기능

즉 내부에서 호출되는 기능과 외부에서 호출되는 기능이 있는데

외부에서 호출되는 메소드는 외부와 관계를 가지고 있다는 의미이고 Interface는  이 외부와의 관계를 정의하는 것이다.

상태와 기능의 decoupling 종속성을 떼내었다.

object는 상태와 기능인데 이 기능 부분을 따로 떼어낸 것을 interface라고 할 수 있다.

코드로 보자면

```go
type a struct{
    상태...
}

func (a *A) MethodA() {
    외부 호출되는 메소드...
}

type aer interface{
    MethodA()
}

B.InternalA()
A.ExternalB()
```

**Interface**의 이름은 대부분 끝에'~er'을 붙여서 짓는다.

상태와 기능을 떼어내면서 확장성을 얻어 낼 수 있게 되었다.



