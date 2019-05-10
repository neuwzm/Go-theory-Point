如前文所述，每次循环迭代字符串s的内容都会更新。`+=`连接原字符串、空格和下个参数，产生新字符串, 并把它赋值给`s`。`s`原来的内容已经不再使用，将在适当时机对它进行垃圾回收。

如果连接涉及的数据量很大，这种方式代价高昂。一种简单且高效的解决方案是使用`strings`包的`Join`函数：

```go
func main() {
    fmt.Println(strings.Join(os.Args[1:], " "))
}
```



---



`Printf`有一大堆这种转换，Go程序员称之为*动词（verb）*。下面的表格虽然远不是完整的规范，但展示了可用的很多特性：

```
%d          十进制整数
%x, %o, %b  十六进制，八进制，二进制整数。
%f, %g, %e  浮点数： 3.141593 3.141592653589793 3.141593e+00
%t          布尔：true或false
%c          字符（rune） (Unicode码点)
%s          字符串
%q          带双引号的字符串"abc"或带单引号的字符'c'
%v          变量的自然形式（natural format）
%T          变量的类型
%%          字面上的百分号标志（无操作数）
```





---



Go语言在net这个强大package的帮助下提供了一系列的package来做这件事情，使用这些包可以更简单地用网络收发信息，还可以建立更底层的网络连接，编写服务器程序。

```
// Fetch prints the content found at a URL.
package main

import (
   "fmt"
   "io/ioutil"
   "net/http"
   "os"
)

func main() {

   url := "http://www.baidu.com"
   resp, err := http.Get(url)
   if err != nil {
      fmt.Fprintf(os.Stderr, "fetch: %v\n", err)
      os.Exit(1)
   }
   b, err := ioutil.ReadAll(resp.Body)
   resp.Body.Close()
   if err != nil {
      fmt.Fprintf(os.Stderr, "fetch: reading %s: %v\n", url, err)
      os.Exit(1)
   }
   fmt.Printf("%s", b)
}
```



并发请求多个URL，goroutine和channel的使用

ps：ioutil.Discard输出流中（译注：可以把这个变量看作一个垃圾桶，可以向里面写一些不需要的数据）

```
// Fetchall fetches URLs in parallel and reports their times and sizes.
package main

import (
   "fmt"
   "io"
   "io/ioutil"
   "net/http"
   "time"
)

func main() {
   start := time.Now()
   ch := make(chan string)
   urls := []string{
      "http://www.baidu.com",
      "http://www.qq.com",
   }
   for _, url := range urls {
      go fetch(url, ch) // start a goroutine
   }
   for range urls {
      fmt.Println(<-ch) // receive from channel ch
   }
   fmt.Printf("%.2fs elapsed\n", time.Since(start).Seconds())
}

func fetch(url string, ch chan<- string) {
   start := time.Now()
   resp, err := http.Get(url)
   if err != nil {
      ch <- fmt.Sprint(err) // send to channel ch
      return
   }
   nbytes, err := io.Copy(ioutil.Discard, resp.Body)
   resp.Body.Close() // don't leak resources
   if err != nil {
      ch <- fmt.Sprintf("while reading %s: %v", url, err)
      return
   }
   secs := time.Since(start).Seconds()
   ch <- fmt.Sprintf("%.2fs  %7d  %s", secs, nbytes, url)
}
```



---

Web服务

Go语言的内置库使得写一个类似fetch的web服务器变得异常地简单。在本节中，我们会展示一个微型服务器，这个服务器的功能是返回当前用户正在访问的URL。比如用户访问的是 <http://localhost:8000/hello> ，那么响应是URL.Path = "hello"。

```go
// Server1 is a minimal "echo" server.
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/", handler) // each request calls handler
    log.Fatal(http.ListenAndServe("localhost:8000", nil))
}

// handler echoes the Path component of the request URL r.
func handler(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "URL.Path = %q\n", r.URL.Path)
}
```





---

指针

**指针：** Go语言提供了指针。指针是一种直接存储了变量的内存地址的数据类型。在其它语言中，比如C语言，指针操作是完全不受约束的。在另外一些语言中，指针一般被处理为“引用”，除了到处传递这些指针之外，并不能对这些指针做太多事情。Go语言在这两种范围中取了一种平衡。指针是可见的内存地址，&操作符可以返回一个变量的内存地址，并且*操作符可以获取指针指向的变量内容，但是在Go语言里没有指针运算，也就是不能像c语言里可以对指针进行加或减操作。