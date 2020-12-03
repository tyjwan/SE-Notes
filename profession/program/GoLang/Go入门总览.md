# Go学习记录
***
*极客时间 
*给人最大的感受是结合了C系和Python*

## Hello World示例
```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World")
}
```

## 约定与规范
- 函数大写开头表示包外可以访问

## 与其他语义的编程差异
- 基础数据结构
  - 赋值可以自动进行类型判断：x := 1
  - 在一个赋值语句中对多个变量进行赋值:a,b = b,a
  - 别名和原有类型都不支持隐式类型转换
  - 不支持指针运算
  - string是值类型，默认初始化为空字符串
  - 操作符
    - 不支持++和--
    - 数值的比较直接比较数值的内容
    - &^ 按位置置零
      - 1 &^ 0 -- 1
      - 1 &^ 1 -- 0
      - 0 &^ 1 -- 0
      - 0 &^ 0 -- 0
   - 只支持for循环
   - if 中支持两端，第一段可以定义值（可调函数）
   - swith：case后自动break；不限制常量或整数；可逗号跟个多个结果选项；可不设条件表达式，类似if...else..

### 编程测试程序
- 1.源码文件以 _test 结尾：xxx_test.go
- 2.测试方法名以Test开头：func TestXXX(t *testthing.T) {...}

## 基本数据结构
### 遍历赋值
- bool
- string
- int int8 int16 int32 int64
- uint unit8 unit16 uint32 unit64
- byte // alias for uint8
- rune // alias for int32,represents a Unicode code point
- float32 float64
- complex64 complex128
- 指针 &a

```go
var x int = 1
x := 1
```

#### 常量定义
```go
// 快速设置连续值,十进制与二进制
const (
	Monday = iota + 1
	Tuesday
	Wednesday
	Thursday
	Firday
	Staturday
	Sunday
)

const (
	Open = 1 << iota
	Close
	Pending
)
```

#### 类型的预定义值
- math.MaxInt64
- math.MaxFloat64
- math.MaxUint32

### 算术符
*与其他相同*

## 常用集合
### 数组与切片
```go
// 声明与初始化，数组大小固定，切片动态大小
var a [3]int //声明并初始化为0
b := [3]int{1, 2, 3}
arr := [...]int{1, 2, 3}
c := [2][2]int{{1, 2}, {1, 2}}
a[0] = 1
arr := make([]int, 3, 5)

// 数组的遍历
for idx, e := range arr {
	fmt.Println(idx, e)
}
for _, e := range arr {
	fmt.Println(e)
}

// 截取和添加，和Python相同,但不支持-1之类的操作
arr[begin:end]
arr.append(x)

// 动态增加，二倍动态增长；且需要注意的是切片后共享存储空间
// 共享的部分导致修改后会导致两者共同的部分会改变
// 切片不可以进行比较
arr := []int{} 
for i:= 0; i<10; i++{
	arr = append(arr, i)
}
```

### Map
```go
// 声明
m := map[string]int{"one": 1}
m := map[string]int{}
m := make(map[string]int, 10)
m[key] == value

// 删除
delete(m, key)

// map为空默认返回0，存在判断
if v, ok = m[3]: ok {
	fmt.Println(v)
}

// 遍历
for k, v := range m {
	fmt.Println(k, v)
}

// map value可以是一个方法
m := map[int]func(op int)int{}
m[1] = func(op int) int {return op * op}

// 没有set，使用map代替
myset := map[int]bool{}
myset[1] = true
if myset[n] {
	fmt.Println("exit")
}
```
  
## 字符串
- string是数据类型，不是引用或者指针
- string是只读的byte slice，len函数可以获取它包含的byte数
- string的byte数组可以存放任何数据

```go
s = "中"
c := []rune(s)

for _, c := range s {
	fmt.Println(c)
}

// 常用函数，切割、拼接、字符串与数字转换
parts := strings.Split(s, ",")
parts := strings.Join(parts, ",")
s := strconv.Itoa(10)
if i, err := strconv.Atoi("10"); err {
	10 + i
}
```

## 函数
- 可以返回多个值
- 所有参赛都是值传递：slice、map、channel会有引用传递的幻觉
- 函数可以作为变量的值
- 函数可以作为参数和返回值

```go
// 普通函数定义
func f() {
	return 1, 2
}

// 函数包函数定义，有点Spring AOP的感觉
func f(inner func(op int) int) func (op int) int  {
	return func(n int) int {
		n += 1
		return n
	}
}

tsSF := timeSpent(slowFun)
fmt.Println(tsSF)

// 可变参数
func sum(ops ...int) int {
	amount := 0
	for _, op := range ops {
		amount += op
	}
	return amount
}

// defer函数（延时）
func clear() {}
func TestDefer(t *testing.T) {
	// 这个函数在最后执行，类似try finally
	defer clear()
	t.Log(xxx)
	panic(err) // 这里会执行，后面的不会执行
}
```

## 面向对象
- 不支持继承
- 接口为非入侵性，实现不依赖于接口定义；接口的定义可以包含在接口使用者包内
- 倾向于使用更小的接口（1个方法）；较大的接口有较小的接口组合而成；只依赖必要功能的最小接口

```go
// 结构体定义、初始化、使用
type Employee struct {
	Id string
	Name string
	Age int
}
e := Employee{"0", "Bob", 20}
e := Employee{Name: "Name", "Age": 30}
e := new(Employee) // 这里返回的是引用/指针
e.Id = "2"

// 行为/方法定义,第二种没有数据的复制，推荐使用
func (e Employee) String() string {
	return e.Id
}

func (e *Employee) String() string {
	return e.Id
}

e := Employee{"0", "Bob", 20}
e.String()

// 接口定义、实现
type Programmer interface {
	WriteHelloWrold() Code
}

type GoProgrammer struct {
	
}

func (p *GoProgrammer) WriterHelloWorld() Code {
	return "hello"
}

var p Programmer
p = new(GoProgrammer)
p.WriterHelloWorld()

// 自定义类型
type mySytyle func()

// 代码复用
type IParent interface {
	func1() int
}

type Child1 struct {}

func (o *child1) func1() int {
	return 1
}

func xxxChild(o IParent) {
	return p.func1()
}

child := new(Child)
xxxChild(child)
```

## 错误机制
- 没有异常机制
- error类型实现了error接口
- 可以通过errors.New来快速创建错误实例
- panic、recover、os.Exit退出时不会调用defer指定的函数；不会输出当前调用栈信息

```go
type error interface {
	Error() string
}
errors.New("xxxxxxxxxxx")

// 错误的定义和使用,使用if的机制避免if？
var err1 = errors.New("xxxx")
func f1(n int) {
	if n < 0 {
		return nil, errors.New("xxxx")
	}
	if n > 0 {
		return nil, err1
	}
}
fmt.pirnt(f1(-10))

// 异常捕捉、恢复、退出
defer func() {
	if err := recover(); err != nil {
		//恢复错误
	}
}
```

## 包和依赖管理
- 基本复用模块单元：以首字母大写表明可以被包外代码访问
- 代码的package可以和所在目录不一致
- 同一个目录里的Go代码的package要保持一致
- init方法：
  - 在main执行前，所有依赖的package的init方法都会被执行
  - 不同包的init函数安装包导入的依赖关系决定执行顺序
  - 每个包可以有多个init函数
  - 包的每个源文件也可以有多个init函数，这个有点特殊
- 如何需要远程使用，直接push目录src后面的文件，才能远程使用
- 依赖管理：用于自动生成依赖配置文件，同包可以使用不同版本
  - godep
  - glide
  - dep
  
### 本地包的导入使用
```go
// file2
package prin

import "fmt"

func Prin()  {
	fmt.Println("Hello World")
}
```
  
```go
// file1
import (
	"../../ch1/prin"
	"fmt"
	"testing"
)

prin.Prin()
```

### 第三方包的导入和使用
```go
// 获取依赖 go get -u
go get url(github)

// 导入
import (
	cm "githup/xxxxxxx"
)
cm.Xxxxxx()
```

## 并发编程
- 初始为2k；对应关系M:N
- 协程：go func(x int) {...}()
- 锁：sync Mutex RWLock
- CSP并发控制：通过channel进行通信，容量有限
- 多路选择与超时

```go
// 简单协程
for i:=0; i<10;i++ {
	go func(i int) {
		
	}(i)
}

// 锁
var mut sync.Mutex
count := 0
for i:=0; i<10;i++ {
	go func() {
		defer func() {
			mut.Unlock
		}
		mut.Lock()
		count += 1
	}()
}

// 同步
var mut sync.Mutex
var wg sync.WaitGroup
count := 0
for i:=0; i<10;i++ {
	wg.Add(1)
	go func() {
		defer func() {
			mut.Unlock
		}
		mut.Lock()
		count += 1
		wg.Done
	}()
}
wg.Wait()

// 异步等待
func AsycService() chan string {
	retch := make(char string, 1) // 使用buff chan不会阻塞
	retch <- "ret"
	return retch
	
}
retCh := AsycService()
fmt.Println(<-retCh)

// 多路选择
select {
    case ret := <-retCh1:
	    t.Logf("result %s", ret)
	case ret := <-retCh2:
		t.Logf("result %s", ret)
	default:
		t.Error("No one return")
}

// 超时控制
select {
    case ret := <-retCh:
    	t.Logf("result %s", ret)
    case <-time.After(time.Second * 1)
        t.Error("time out")
}

// 通道生产消费与取消，元素没有重复消费
func dataProducer(ch chan int, wg *sync.WaitGroup) {
	go func() {
		for i := 0; i < 10; i++ {
			ch <- i
		}
		close(ch)
		wg.Done()
	}()
}

func dataReceiver(ch chan int, wg *sync.WaitGroup) {
	go func() {
		for {
			if data, ok := <-ch; ok {
				fmt.Println(data)
			} else {
				break
			}

		}
		wg.Done()
	}()
}

func TestFibList(t *testing.T) {
	var wg sync.WaitGroup
	ch := make(chan int)
	wg.Add(1)
	dataProducer(ch, &wg)
	wg.Add(1)
	dataReceiver(ch, &wg)
	wg.Add(1)
	dataReceiver(ch, &wg)
	wg.Wait()
}

// 任务的取消
func isCancelled(cancelChan chan struct{}) bool {
	select {
	case <-cancelChan:
		return true
	default:
		return false
	}
}

func cancel_1(cancelChan chan struct{}) {
	cancelChan <- struct{}{}
}

func cancel_2(cancelChan chan struct{}) {
	close(cancelChan)
}

func TestCancel(t *testing.T)  {
	cancelChan := make(chan struct{}, 0)
	for i := 0; i < 5; i++ {
		go func(i int, cancelCh chan struct{}) {
			for {
				if isCancelled(cancelCh) {
					break
				}
				time.Sleep(time.Millisecond * 5)
			}
			fmt.Println(i, "Done")
		}(i, cancelChan)
	}
	cancel_1(cancelChan) // 只能单个
	cancel_2(cancelChan) // 广播
	time.Sleep(time.Second * 1)
}

// 父子层级线程取消
func isCancelled(ctx context.Context) bool {
	select {
	case <-ctx.Done():
		return true
	default:
		return false
	}
}

func TestCancel(t *testing.T)  {
	ctx, cancel := context.WithCancel(context.Background())
	for i := 0; i < 5; i++ {
		go func(i int, ctx context.Context) {
			for {
				if isCancelled(ctx) {
					break
				}
				time.Sleep(time.Millisecond * 5)
			}
			fmt.Println(i, "Done")
		}(i, ctx)
	}
	cancel()
	time.Sleep(time.Second * 1)
}

// 单例模式，懒汉线程安全
type Singleton struct {}
var singletonInstance *Singleton
var once sync.Once
func GetSingletonOgj() *SingletonObj {
	once.Do(func() {
		fmt.Print("Create Singleton obj")
		obj = new(Singleton)
	})
	return obj
}

// 仅需任意任务完成
func runTask(id int) string {
	return "1"
}
func firstResponse() string {
	numOfRunner := 10
	ch := make(chan string, numOfRunner)
	for i := 0; i < numOfRunner; i++ {
		go func(i int) {
			ret := runTask(i)
			ch <- ret
		}(i)
	}
	return <-ch
}
func Test() {
	fml.print("before", runtime.NumGoroutine)
	firstResponse()
	time.sleep(1)
	fml.print("After", runtime.NumGoroutine)
}


// 所有任务完成
func runTask(id int) string {
	return "1"
}
func firstResponse() string {
	numOfRunner := 10
	ch := make(chan string, numOfRunner)
	for i := 0; i < numOfRunner; i++ {
		go func(i int) {
			ret := runTask(i)
			ch <- ret
		}(i)
	}
	for j := 0; j < numofRunner; j++ {
		<- ch
	}
	return "1"
}
func Test() {
	fml.print("before", runtime.NumGoroutine)
	firstResponse()
	time.sleep(1)
	fml.print("After", runtime.NumGoroutine)
}

// 对象池,使用buffer channel实现
type ReuseableObj struct{}
type objPool stuct {
	bufChan chan *ReuseableObj
}
func newObjPool(num int) *objPool {
	objPool := ObjPool{}
	objpool.bufChan = make(chan *ReuseableObj, num)
	for i:=0; i<num;i++ {
		objpool.bufchan <- &ReuseableObj{}
	}
	return &objpool
}
func (p *Objpool) GetObj(timeout time.Duration) (*ReuseableObj, error) {
	select {
	case ret:=<-p.buffchan:
		return ret, nil
		case <-time.After(timeout):
			return nil, errors.new("timeout")
	}
}
func (p *ObjPool) releaseObj(obj *ReuseableObj) error {
	select {
	case p.buffchan <- obj:
		return nil
		default:
			return errors.new("overflow")
	}
}
pool := newObjPool(10)
o := GetObj(1)
releaseObj(o)

// sync.pool对象缓存,GC会清楚sync.Pool缓存的对象；有锁的开销
pool := &sync.Pool {
	new : func() interface{} {
		return 0
	}
}
array := pool.Get().(int)
pool.Put(10)
```

## 单元测试
- error:后面测试仍会执行
- failt：后面测试不会执行
- 代码测试覆盖率：go test -v -conver
- 断言：https://github.com/stretchr/testify
- benchmark:性能测试;go test -bench=. -benchmem
- BDD in go：
  - https://github.com.smartystreets/goconvey
  - 启动：$GOPATH/bin/goconvey

```go
// 单元测试
func f() {}
func Tsetf() {
	input := [...]{1, 2, 3}
	expect := [...]{1, 3, 3}
	xxxxxxxxxx...xxxxxxxxxx
	assert.Equal(t, input[i], expect[i])
}

// benchmark
func Benchmark(b *testing.B) {
	b.Resettimer()
	xxxxxxx
	b.stopxx()
}
```

## 反射与unsafe
- reflect.TypeOf返回类型
- reflect.ValueOf返回值
- 可以从reflect.Value获取类型
- 通过kind的来判断类型
- 按名字访问结构的成员：reflect.ValueOf(*e).FieldByName("Name")
- 按名字访问结构的方法：reflect.ValueOf(e).MethodByName("UpdateAge").Call([]reflect.Value{reflect.ValueOf(1)})

```go
func CheckTupe(v interface{}) {
	t := reflect.Type(v)
	switch t.Kind() {
	case reflect.Float32:
		xxxxx
	}
}
```

## 常用框架的实现
- pipe-filter-framework
- micro-kernel-framework

## 常见任务
- 内置json解析：常用内部配置文件，其他不适合，反射性能低
- easyjson:第三方库
- HTTP服务：内置的net/http
- 构建RESTful服务：第三方的httprouter

## 性能调优
### 性能分析工具
- 安装 graphviz
- 设置go环境变量
- 安装go-torch：1.11已经内置

- pprof：代码内嵌文件输出，感觉还挺好用的
- HTTP方式输出profile

### 常见分析指标
- Wall time
- CPU time
- block time
- memory time
- gc time / time spent

### 性能注意项
- 只读锁并不是不消耗性能
- sync.Map不是线程安全的，适合读多写少，key较稳定
- concerrentMap
- disructry

### GC友好的代码
- 避免内存分配和复制
- 复杂对象尽量传递指针
- 复用内存

- 相关工具
  - GC日志
  - go tool trace
  
## 高可用性服务设计
- 高效字符串连接
- 面向错误的设计
- 面向恢复的设计
- Chaos Engineering


## 参考链接
https://pkg.go.dev/
https://golang.org/
https://golang.google.cn/doc/effective_go.html

- [GO文档](http://docscn.studygolang.com/doc/)
- [欢迎使用 Go 指南](https://tour.go-zh.org/list)
- [Effective Go](http://docscn.studygolang.com/doc/effective_go.html)