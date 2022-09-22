## Задача 1

```shell
vagrant@server1:~$ go version
go version go1.19.1 linux/amd64
```

## Задача 2
Ознакомился

## Задача 3. Написание кода

#### Напишите программу для перевода метров в футы `(1 фут = 0.3048 метр)`. Можно запросить исходные данные у пользователя, а можно статически задать в коде. Для взаимодействия с пользователем можно использовать функцию Scanf:

```shell
vagrant@server1:~/go/src/footmeter$ cat footmeter.go
```
```gotemplate
package main

import "fmt"

import "math"

        func main() {
            fmt.Print("Foots value: ")
            var input float64

            fmt.Scanf("%f", &input)
            output := input * float64(0.3048)
            rOutput := math.Round(output)
            sOutput := fmt.Sprintf("( %.2f)", output)
            fmt.Println("Meters value:", rOutput, sOutput )
        }
```
```shell
vagrant@server1:~/go/src/footmeter$ go build
vagrant@server1:~/go/src/footmeter$ ./footmeter
Foots value: 77
Meters value: 23 ( 23.47)
```
### Напишите программу, которая найдет наименьший элемент в любом заданном списке, например: `x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17,}`
```shell
vagrant@server1:~/go/src/minfind$ cat minfind.go
```
```gotemplate
package main

import "fmt"

func main() {
        x := []int{48,96,86,68,57,82,63,70,37,34,83,27,19,97,9,17}
        var min int = x[0]

        for i := 0; i < len(x); i++ {
                if x[i] < min {
                        min = x[i]
                }
        }
        fmt.Println(min)
}
```
```shell
vagrant@server1:~/go/src/minfind$ go build
vagrant@server1:~/go/src/minfind$ ./minfind
9
```
### Напишите программу, которая выводит числа от 1 до 100, которые делятся на 3. То есть `(3, 6, 9, …)`.
```shell
vagrant@server1:~/go/src/find$ cat find.go
```
```gotemplate
package main

import "fmt"

func main() {
        for i := 1; i <= 100; i++ {
                if i % 3 == 0 {
                        fmt.Printf("%d,", i)
                }
        }
}
```
```shell
vagrant@server1:~/go/src/find$ go build
vagrant@server1:~/go/src/find$ ./find
3,6,9,12,15,18,21,24,27,30,33,36,39,42,45,48,51,54,57,60,63,66,69,72,75,78,81,84,87,90,93,96,99,
```
