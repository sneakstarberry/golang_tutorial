# Slice

## 다른 언어의 경우
> **동적 배열**
C++ 은 STL vector
Java ArrayList 
C# List
Python Slice //원조
Golang Slice

## 정적 배열/동적 배열
> ### 정적배열
>정적 배열은 길이가 변화하지 않는다.
> ### 동적 배열
>동적 배열은 길이가 늘어날 수 있다.

## 동적 배열은 어떻게 생길까?
우선 할당된 메모리는 증가 할 수 없다.

>그 이유로는 옆 자리에 메모리가 생길 수도 있기 때문에
처음 받은 공간에서 늘릴 수 없다.

그렇다면 어떻게 할 것인가?

>처음 공간을 받고 더 늘어났을 때 다시 공간을 할당 받는다.
이후 처음 공간을 삭제한다. 이러한 방식으로 배열의 길이를 늘린다.

동적 배열은 고정 길이의 메모리를 가르키고 있는 상태이다.

>[]int  ----> □□□ 메모리
늘어 났을때  ....> □□□□□□ 늘어난 메모리
처음 메모리를 가르키다가 새로운 배열이 생기면 그 곳의 메모리를 가르키고 처음 메모리를 지운다.

얼마나 늘어나는가?
>□ --> □□ --> □□□□
**두배 씩** 증가한다.
이렇게 함으로써 어느정도 여유분을 확보한다.

#### 하는 방법
<pre><code>
var a []int
a := []int {1,2,3,4}
a := make([]int, 3)    //초기 길이는 3
a := make([]int, 0, 8) //초기 길이(length)는 0이고 한도(capacity)는 8이다.
</code></pre>
길이와 한도의 차이는 무엇인가?
> 길이 : 실제로 사용하고 있는 공간
한도 : 실제로 사용하지는 않지만 확보해 둔 공간

##### 빈 동적 배열
<pre><code>
package main

import "fmt"

func main() {
	//빈 동적 배열
	var a []int

	fmt.Printf("len(a) = %d\n", len(a)) //0
	fmt.Printf("cap(a) = %d\n", cap(a)) //0
...
</code></pre>

##### 값을 넣었을 때 길이와 한계
<pre><code>
...
	b := []int{1, 2, 3, 4, 5}

	fmt.Printf("len(b) = %d\n", len(b)) //5
	fmt.Printf("cap(b) = %d\n", cap(b)) //5
...
</code></pre>
##### 길이와 한계를 정해줬을 때
<pre><code>
...
	c := make([]int, 0, 8)

	fmt.Printf("len(c) = %d\n", len(c)) //0 배열의 길이
	fmt.Printf("cap(c) = %d\n", cap(c)) //8 배열의 한계
...
</code></pre>
#### append 시 메모리의 변화
##### 값을 append 했을 때 길이와 한계의 변화
<pre><code>
...
	d := make([]int,1 , 2)
	d = append(d, 1,2,3,4,5,6)

	fmt.Printf("len(d) = %d\n", len(d)) //7
	fmt.Printf("cap(d) = %d\n", cap(d)) //8
	
	//길이 초과로 새로운 메모리 생성으로두개의 주소가 달라진다.
	//두개의 한계 또한 달라지는데 길이가 늘어날때 한계는 더 많이 늘어나는 것을 볼 수 있다.
...
</code></pre>
##### append 시 메모리 주소와 두 값의 변화
###### 값이 초기 한계를 초과할 때
<pre><code>
...
	e := make([]int,2 , 4)
	f := append(e, 1,2,3,4,5)

	fmt.Printf("e의 주소= %p\n", e) // e의 주소= 0xc000012110 이 둘이 다르다는 것은
	fmt.Printf("f의 주소= %p\n", f) // f의 주소= 0xc00000a360 서로 다른 메모리 할당을 받았다는 증거
	fmt.Printf("e의 주소= %d\n", e) // e와 f 두개의
	fmt.Printf("f의 주소= %d\n", f) // slice(동적배열)가 만들어진것이다.
	
	f[0] = 1
	f[1] = 2
	
	fmt.Printf("%d %d\n", e, f) // e:[0 0] f:[1 2 1 2 3 4 5] e의 값이 변하지 않는 이유는 서로 다른 메모리를 참조하고 있기 때문이다.
...
</code></pre>
###### 값이 초기 한계를 초과하지 않을 때
<pre><code>
...
    g := make([]int, 2,4)
	h := append(g,3)

	fmt.Printf("%p %p\n", g, h) // 0xc000010420 0xc000010420 둘의 주소가 같다. 
	fmt.Printf("%d %d\n", g, h) // 이는 append할 때 새로 만들 메모리를 만들 필요가 없어서 같은 메모리를 가르킨다.
	
	h[0] = 1
	h[1] = 2
	
	fmt.Printf("%d %d\n", g, h) // g:[1 2] h:[1 2 3] g의 값이 변하는 이유는 서로 같은 메모리를 참조하고 있어서이다.
...
</code></pre>
##### 주의
이렇듯 동적 배열을 사용하게 되면 초기 값을 바꿀 수 있기 때문에
아래와 같이 쓸 수 있다.
<pre><code>
    //실전 동적 배열
	p := make([]int, 2,4)
	p[0] = 1
	p[1] = 2
	q := make([]int, len(p))

	for i:=0; i<len(p);i++ {
		q[i] = p[i]
	}
	
	q = append(q, 3)
	
	fmt.Printf("%p %p\n", p,q)
	
	q[0] = 4
	q[1] = 5
	
	fmt.Println( p,"  ",q) // p:[1 2] q:[4 5 3]
</code></pre>
이와 같이 쓰면 초기 값을 변경시키지 않고 쓸 수 있다.
같은 변수를 사용하면 상관없지만
다른 변수를 사용하면 다른 변수가 변경될 수 있기 때문에 이렇게 쓴다.

### Slice 2
##### Slice 사용해보기
기본사용
<pre><code>
...

    a := []int{1~10} // a의 배열이 1부터 10까지 있을 때
    //a[StartIdx : EndIdx]
    a[4:8] //5,6,7을 가져온다.
...
</code></pre>
활용 예제
<pre><code>
package main

import "fmt"

func main() {
	a := []int{1,2,3,4,5,6,7,8,9,10}

	b := a[4:8]
	
	c := a[4:]
	
	d := a[:4]
	
	fmt.Println(a) // a:[1 2 3 4 5 6 7 8 9 10]
	fmt.Println(b) // b:[5 6 7 8]
	fmt.Println(c) // c:[5 6 7 8 9 10]
	fmt.Println(d) // d:[1 2 3 4]
}
</code></pre>
###### Slice는 어떻게 동작할까?
우선 실제로 자르는 것은 아니다.
<pre><code>
    a:= []int{1,2,3,4,5,6,7,8,9,10}
    b:= a[4:8] // 그 배열을 가르키는 포인터이다.
</code></pre>
a는 배열의 처음을 가르키고 있고
b는 배열의 일부분을 가르키는 것이다.
<pre><code>
    a:= []int{1,2,3,4,5,6,7,8,9,10}
    b:= a[4:8]

    b[0] = 1
    b[1] = 2
    
    fmt.Println(a) //[1 2 3 4 1 2 7 8 9 10] a의 배열이 바뀐다. b가 a의 주소를 가르키고 있기 때문이다.
</code></pre>
###### 활용
뒤의 5개 값을 없앤다.
<pre><code>
package main

import "fmt"

func RemoveBack(a []int) []int {
	return a[:len(a)-1]
}

func main() {
	a := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}

	for i := 0; i < 5; i++ {
		a = RemoveBack(a)
	}
	
	fmt.Println(a)
}

</code></pre>
