Garbage Collector
===================

## 알아야 될 것

변수 => 메모리

값을 담는 그릇이다.

<pre><code>

var a int //주소

var p *int //주소를 가르킨다.

p=&a

</code></pre>

## Memory Garbage 란?

> 메모리에 쓰레기가 쌓인다는 것은
변수를 지정하고 아무도 관심을 쓰지 않는다면 메모리가 부족해진다.
이것을 'Memory Garbage' 라고 할 수 있다.
<pre><code>
var p *int
var a int
p= &a
</code></pre>
> 를 만들고 p를 참조하지 않는다면 그 자리는 쓸 수 없게 된다.

  

# C언어의 경우

## 스택 메모리와 힙 메모리 두가지 형태가 존재한다.
### 스택 메모리의 경우

>일반적인 변수가 할당되고 아래서 부터 쌓이는 형태로 메모리가 쌓인다.
### 힙 메모리의 경우

>malloc{memory unlock의 약자}(사이즈)를 요구하면 그에 해당하는 메모리를 할당해준다.
주소를 참조하고 주소를 반환해 준다.
### 버그가 일어나는 이유
>p = malloc(100) 으로 100byte를 할당 받았을 때 C언어에서는 반드시 지워줘야한다.
free  (p ); 항상 쌍으로 발생해야 한다.
버그 때문에 혹은 지우는 것을 깜박하거나 참조 주소가 사라졌을 때 메모리에 쓰레기가 쌓인다.(memeory leak이라고 한다.)
if문 안에서 p 변수를 malloc할당하고 if문을 벗어나면 변수 p가 사라진다.
Memory leak--> Bug 흔히 일어나는 버그이다.
그래서 나온 것이 Garbage Collector이다.
GC가 쓰레기 메모리를 정리해준다.

  

## 작동원리

>**쓸모없어지면 쓰레기**

ex)
<pre><code>func add() {
var a int
a = 3
</code></pre>

이 안을 넘어가면 a는 **쓰레기**가 된다.

  

# 어떻게 아는가?

>기본:

>Reference Count 횟수가 0이 되는 것을 통해서

//ex)
<pre><code>
var a int
var p *int
p= &a
a=3
*p==3

</code></pre>
Reference Count(참조 횟수)의 횟수가 0이 되면 그것은 쓰레기 데이터이다.
<pre><code>
ex)

runc add() *int {

var a int

var p *int

a=3

p=&a

return p

}

v := add()
</code></pre>

>v가 a의 변수를 참조 하고 있는 것으로 결과가 남고
p변수의 데이터는 GC에 의해 사라지고 v와 a의 데이터만 남는다.

  

## In Golang GC 작동원리

<pre><code>

func add() *int

var a int

var p *int

a=3

p=&a

return p

}

v:= add()

</code></pre>

v가 a를 참조하는 것만 마지막

까지 남는다.(참조하지 않는 것은 **삭제**한다.)

  
  

# 참조 횟수가 0이 아닌 경우:

>**외딴 섬**이라고 지칭한다.
a ==> b ==> c ==> a
자기들 끼리만 서로 참조 한다. 이 또한 Memory Leak이다.
이 또한 GC가 삭제한다.
  

하지만 GC가 하는 일이 굉장히 많기 때문에 속도가 느려진다.

기술발전으로 인하여 GC로 인해 속도가 느려지는 지도 모른다.