# Slice 심화
### Slice의 구조
>Slice는
pointer * --> 시작주소
len **int** --> 갯수
cap **int** --> 해당 메모리의 최대 갯수
로 이루어져 있다고 할 수 있다.

Slice가 가르키는 곳
>Data는 저장이 되어야한다.
메모리 상에 data가 저장이 된다.
data는 메모리 상에 배열을 이루고 있는데
slice는 그 배열의 시작을 가르키고 있는 것(pointer)이다.

<pre><code>
package main

import "fmt"

func main() {
    var s []int

    s = make([]int, 3)

    s[0] = 100
    s[1] = 200
    s[2] = 300

    fmt.Println(s, len(s), cap(s)) // [100 200 300], 3, 3

    s = append(s, 400, 500, 600, 700)

    fmt.Println(s len(s), cap(s)) // [100 200 300 400 500 600 700], 7, 8
    
}
</code></pre>