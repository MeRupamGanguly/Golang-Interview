# Golang-Interview

## Introduce Yourself
I have a background in BTech IT. My first company was Sensibol, where I worked as a Golang Backend Developer. My primary role involved developing microservices using Golang, AWS, MongoDB, and Redis. Currently, I am working at Calsoft for Extreme Networks, focusing on a project related to data center automation. In this project, I work with technologies such as Golang, RabbitMQ, Kubernetes, MySQL, and GoSwitch.

## What Projects Have You Worked On?
- **PDL (Phonographic Digital Limited)**: A music distribution and royalty management platform.
- **Singshala**: A platform similar to TikTok but with the added feature of analyzing the audio components of videos and providing rankings based on that analysis.
- **Data Center Automation**: In this project, we create profiles that are essentially configurations applied to switch ports, and bind those configurations to the ports.

All of these projects utilized event-driven microservices architectures.

---

## Microservices vs Monolith
- **Microservices** are better for large projects where scaling and almost zero downtime are required. Bug fixing and maintaining the codebase are easier. A disadvantage of microservices can be inter-service network calls.
- **Monolith** is suitable for smaller projects but can become difficult to scale or maintain as the application grows.

---

## Authentication vs Authorization
- **Authentication** is about verifying identity. Users typically provide credentials such as a username and password, or biometric data, and the system checks these credentials against stored data to confirm their validity.
- **Authorization** is about granting permissions based on the verified identity. The system checks the user’s permissions and roles to determine what they can access or modify.

---

## Golang Garbage Collection
Golang uses automatic garbage collection to manage memory. Developers do not need to allocate or deallocate memory manually, which reduces memory-related errors.

---

## Pointer
A **pointer** holds the memory address of a variable, struct, function, or any data type.

- You can reference a variable to get its address using the `&` operator.
- You can dereference a pointer to access the value at the memory address it points to using the `*` operator.

```go
package main

import "fmt"

func main() {
    a := 9           // Declare an integer variable 'a'
    p := &a          // 'p' is a pointer that stores the address of 'a' (referencing)

    fmt.Println("Address of a:", &a)    
    // Outputs the memory address of 'a'
    fmt.Println("Value of p:", p)       
    // Outputs the memory address stored in p
    fmt.Println("Value at address p:", *p) 
    // Dereferencing p to get the value (outputs: 9)

    *p = 10          // Change the value of 'a' using the pointer
    fmt.Println("New value of a:", a)   // Outputs: New value of a: 10
}
```

### Goroutine vs Thread:
Goroutines are designed for concurrency, meaning multiple tasks can run using context switching. Threads are designed for parallelism, meaning multiple tasks can run simultaneously on multiple CPU cores.

Goroutines have a dynamic stack size and are managed by the Go runtime. Threads have a fixed-size stack and are managed by the OS kernel.

### What is Closure in golang:
A closure is a special type of anonymous function that can use variables, that declared outside of the function. Closures treat functions as values, allowing us to assign functions to variables, pass functions as arguments, and return functions from other functions. 

```go
v:= func (f func (i int) int) func()int{
    c:=f(3)
    return func()int{
        c++
        return c
    }
}
f:=v()// v() returns a function address
f()// call the function which v() returns
```

### Interfaces in golang:
Interfaces allow us to define contracts, which are abstract methods, have no body/implementations of the methods.
A Struct which wants to implements the Interface need to write the body of every abstract methods the interface holds.
We can compose interfaces together.
An empty interface can hold any type of values.
name.(type) give us the Type the interface will hold at runtime. or we can use reflect.TypeOf(name)
```go
func ty(i interface{}) {
	switch i.(type) {
	case int:
		fmt.Println("Integer")
	default:
		fmt.Println("No idea")
	}
	fmt.Println(reflect.TypeOf(i))
}
func main() {
	ty(67.89)
}
```
### Panic Defer Recover combo:
panic is use to cause a Runtime Error and Stop the execution.
When a function return or panicking then Defer blocks are called according to Last in First out manner, the last defer will execute first.
Recover is use to regain the execution from a panicking situation and handle it properly then stop execution. Recover is useful for close any connection like db and websockets etc.
```go
func div(num int) int {
	if num == 0 {
		panic("Not divisible by 0")
	} else {
		return 27 / num
	}
}
func rec() {
	r := recover()
	if r != nil {
		fmt.Println("I am recovering from Panic")
		fmt.Println("I am Fine Now")
	}
}
func main() {
	defer rec()
	fmt.Println(div(0))
	fmt.Println("Main Regained") // Will not executed if divisble by 0
}
```
### Array vs Slice: 
Array can not Grow and Shrink dynamically at runtime, Slice can. Slice is just references to an existing array of a fixed length.

### Method Dispatching:
golang use Receiver function for method dispatching and has 2 way to dispatch methods at runtime.

Pointer receiver function: As obj is refrence of the Struct so any modification inside the function will affect the original Struct. More memory-efficient and can result in faster execution, especially for large structs.
```go
func (obj *class_name)method_name(argument int) (returns_name bool){
    //body
}
```
Value receiver function: As obj is copy of the Struct so any modification inside the function will not affect the original Struct. 
```go
func (obj class_name)method_name(argument int) (returns_name bool){
    //body
}
```
### Concurency Primitives:
Concurency Primitives are tools that are provided by any programming languages to handle execution behaviors of Concurent tasks.

In golang we have Mutex, Semaphore, Channels as concurency primitives.

Mutex is used to protect shared resources from being accessed by multiple threads simultaneously.

Semaphore is used to protect shared pool of resources from being accessed by multiple threads simultaneously. Semaphore is a Counter which start from Number of Reosurces. When one thread using the reosurces Semaphore decremented by 1. If semaphore value is 0 then thread will wait untils its value greater than 0. When one thread done with the resources then Semaphore incremented by 1.

Channel is used to communicate via sending and receiving data and provide synchronisation between multiple gorountines. If channel have a value then execution blocked until reader reads from the channel.
Channel can be buffered, allowing goroutines to send multiple values without blocking until the buffer is full. 

Waitgroup is used when we want the function should wait until goroutines complete its task.
Waitgroup has Add() function which increments the wait-counter for each goroutine.
Wait() is used for wait until wait-counter became zero.
Done() decrement wait-counter and it called when goroutine complete its task.

### Map Synchronisation:
In golang if multiple goroutines try to acess map at same time, then the operations leads to Panic for RACE or DEADLOCK (fatal error: concurrent map read and map write).
So we need proper codes for handeling Map.
We use MUTEX for LOCK and UNLOCK the Map operations like Read and Write. 
```go
func producer(m *map[int]string, wg *sync.WaitGroup, mu *sync.RWMutex) {
	vm := *m
	for i := 0; i < 5; i++ {
		mu.Lock()
		vm[i] = fmt.Sprint("$", i)
		mu.Unlock()
	}
	m = &vm
	wg.Done()
}
func consumer(m *map[int]string, wg *sync.WaitGroup, mu *sync.RWMutex) {
	vm := *m
	for i := 0; i < 5; i++ {
		mu.RLock()
		fmt.Println(vm[i])
		mu.RUnlock()
	}
	wg.Done()
}
func main() {
	m := make(map[int]string)
	m[0] = "1234"
	m[3] = "2345"
	wg := sync.WaitGroup{}
	mu := sync.RWMutex{}
	for i := 0; i < 5; i++ {
		wg.Add(2)
		go producer(&m, &wg, &mu)
		go consumer(&m, &wg, &mu)
	}
	wg.Wait()
}
```

### Describe Channel comunication

```go
package main

import (
	"fmt"
	"sync"
)

func print(wg *sync.WaitGroup) {
	defer wg.Done() // Decrement the counter when the goroutine completes
	fmt.Println("msg")
}

func main() {
	wg := sync.WaitGroup{}
	wg.Add(1)     // Increment the counter for the goroutine
	go print(&wg) // Start the goroutine
	wg.Wait()     // Wait for the goroutine to finish
}
```

```go
func producer(ch chan<-int){ //ch <- value // Sends value into channel ch
	for i:=0;i<5;i++{
		ch<-i
	}
	close(ch)
}
func consumer(ch <-chan int){ //value := <-ch // Receives from channel ch and assigns it to value
	for n:=range ch{
		fmt.Print("RECV: ",num)
	}
}
for main(){
	ch:=make(chan int)
	go producer(ch)
	consumer(ch)
}
```
### Describe Channel comunication with task distributions:
Lets imagine we have a number n, we have to find 0 to n numbers are prime or not.
```go
func isPrime(n int) bool {
	if n <= 1 {
		return false
	}
	for i := 2; i < n; i++ {
		if n%i == 0 {
			return false
		}
	}
	return true
}
func primeHelper(a []int, ch chan<- map[int]bool, wg *sync.WaitGroup) {
	time.Sleep(time.Second)
	defer wg.Done()
	m := make(map[int]bool)
	for i := range a {
		m[a[i]] = isPrime(a[i])
	}
	ch <- m
}
func main() {
	startTime := time.Now()
	var wg sync.WaitGroup
	n := 12
	arr := []int{}
	for i := 0; i < n; i++ {
		arr = append(arr, i)
	}
	length := len(arr)
	goroutines := 4
	part := length / goroutines
	ch := make(chan map[int]bool, goroutines)
	ma := make(map[int]bool)
	for i := 0; i < goroutines; i++ {
		wg.Add(1)
		s := i * part
		e := s + part
		if e > length {
			e = length
		}
		go primeHelper(arr[s:e], ch, &wg)
	}
	wg.Wait()
	close(ch)
	for i := range ch {
		for k, v := range i {
			ma[k] = v
		}
	}
	fmt.Println(ma)
	fmt.Println("Time Taken: ", time.Since(startTime))
}
```

### Select Statement:
Assume a development scenerio where we have 3 s3 Buckets. We spawn 3 GO-Routines each one uploading a File on each S3 bucket at same time. We have to Return SignedUrl of the file so user can stream the File as soon as possible. Now we do not have to wait for 3 S3 Upload operation, when one s3 upload done we can send the SignedUrl of the File to the User so he can Stream. And Other two S3 Upload will continue at same time. This is the Scenerio when Select Statement can help.

Select statement is used for Concurency comunication between multiple goroutines. Select have multiple Case statement related to channel operations. Select block the execution unitl one of its case return. If multiple case returns at same time, then one random case is selected for returns. If no case is ready and there's a default case, it executes immediately. If there's no default case, select blocks until at least one case is ready.
```go
func work(ctx context.Context, ch chan<- string) {
	rand.NewSource(time.Now().Unix())
	r := rand.Intn(6)
	t1 := time.Duration(r) * time.Second
	ctx, cancel := context.WithTimeout(ctx, t1)
	defer cancel()
	select {
	case <-time.After(t1):
		ch <- "Connection established"
	case <-ctx.Done():
		ch <- "Context Expired"
	}
}

func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)
	go work(context.Background(), ch1)
	go work(context.Background(), ch2)
	select {
	case res := <-ch1:
		fmt.Println("ch1 ", res)
	case res := <-ch2:
		fmt.Println("ch2 ", res)
	}
}
```



### SOLID Principles:
SOLID priciples are guidelines for designing Code base that are easy to understand maintain and extend over time.

Single Responsibility:- A Struct/Class should have only a single reason to change. Fields of Author shoud not placed inside Book Struct.
```go
type Book struct{
  ISIN string
  Name String
  AuthorID string
}
type Author struct{
  ID string
  Name String
}
```
Assume One Author decided later, he does not want to Disclose its Real Name to Spread. So we can Serve Frontend by Alias instead of Real Name. Without Changing Book Class/Struct, we can add Alias in Author Struct. By that, Existing Authors present in DB will not be affected as Frontend will Change Name only when it Founds that - Alias field is not empty.
```go
type Book struct{
  ISIN string
  Name String
  AuthorID string
}
type Author struct{
  ID string
  Name String
  Alias String
}

```
Open Close:- Struct and Functions should be open for Extension but closed for modifications. New functionality to be added without changing existing Code.
```go
type Shape interface{
	Area() float64
}
type Rectangle struct{
	W float64
	H float64
}
type Circle struct{
	R float64
}
```
Now we want to Calculate Area of Rectangle and Circle, so Rectangle and Circle both can Implements Shape Interface by Write Body of the Area() Function.
```go
func (r Rectangle) Area()float64{
	return r.W * r.H
}
func (c Circle)Area()float64{
	return 3.14 * c.R * c.R
}
```
Now we can create a Function PrintArea() which take Shape as Arguments and Calculate Area of that Shape. So here Shape can be Rectangle, Circle. In Future we can add Triangle Struct which implements Shape interface by writing Body of Area. Now Traingle can be passed to PrintArea() with out modifing the PrintArea() Function.
```go
func PrintArea(shape Shape) {
	fmt.Printf("Area of the shape: %f\n", shape.Area())
}

// In Future
type Triangle struct{
	B float64
	H float54
}
func (t Triangle)Area()float64{
	return 1/2 * t.B * t.H
}

func main(){
	rect:= Rectangle{W:5,H:3}
	cir:=Circle{R:3}
	PrintArea(rect)
	PrintArea(cir)
	// In Future
	tri:=Triangle{B:4,H:8}
	PrintArea(tri)
}
```
Liskov Substitution:- Super class Object can be replaced by Child Class object without affecting the correctness of the program.
```go
type Bird interface{
	Fly() string
}
type Sparrow struct{
	Name string
}
type Penguin struct{
	Name string
}
```
Sparrow and Pengin both are Bird, But Sparrow can Fly, Penguin Not. ShowFly() function take argument of Bird type and call Fly() function. Now as Penguin and Sparrow both are types of Bird, they should be passed as Bird within ShowFly() function.
```go
func (s Sparrow) Fly() string{
	return "Sparrow is Flying"
}
func (p Penguin) Fly() string{
	return "Penguin Can Not Fly"
}

func ShowFly(b Bird){
	fmt.Println(b.Fly())
}
func main() {
	sparrow := Sparrow{Name: "Sparrow"}
	penguin := Penguin{Name: "Penguin"}
  // SuperClass is Bird,  Sparrow, Penguin are the SubClass
	ShowFly(sparrow)
	ShowFly(penguin)
}
```
Interface Segregation:- A class should not be forced to implements interfaces which are not required for the class. Do not couple multiple interfaces together if not necessary then. 
```go
// The Printer interface defines a contract for printers with a Print method.
type Printer interface {
	Print()
}
// The Scanner interface defines a contract for scanners with a Scan method.
type Scanner interface {
	Scan()
}
// The NewTypeOfDevice interface combines Printer and Scanner interfaces for
// New type of devices which can Print and Scan with it new invented Hardware.
type NewTypeOfDevice interface {
	Printer
	Scanner
}
```

Dependecy Inversion:- Class should depends on the Interfaces not the implementations of methods.

```go
// The MessageSender interface defines a contract for 
//sending messages with a SendMessage method.
type MessageSender interface {
	SendMessage(msg string) error
}
// EmailSender and SMSClient structs implement 
//the MessageSender interface with their respective SendMessage methods.
type EmailSender struct{}

func (es EmailSender) SendMessage(msg string) error {
	fmt.Println("Sending email:", msg)
	return nil
}
type SMSClient struct{}

func (sc SMSClient) SendMessage(msg string) error {
	fmt.Println("Sending SMS:", msg)
	return nil
}
type NotificationService struct {
	Sender MessageSender
}
```
The NotificationService struct depends on MessageSender interface, not on concrete implementations (EmailSender or SMSClient). This adheres to Dependency Inversion, because high-level modules (NotificationService) depend on abstractions (MessageSender) rather than details.
```go
func (ns NotificationService) SendNotification(msg string) error {
	return ns.Sender.SendMessage(msg)
}
func main() {
	emailSender := EmailSender{}

	emailNotification := NotificationService{Sender: emailSender}

	emailNotification.SendNotification("Hello, this is an email notification!")
}
```

### Create Post and Get APIs:
```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"net/url"

	"github.com/gorilla/mux"
)

// ---------- Transport -----------

type httpTransport struct {
	Service // Interfaces use for Dependency Inversion
}

func NewHttpTransport(s Service) *httpTransport {
	return &httpTransport{
		Service: s,
	}
}

type AddReq struct {
}
type AddRes struct {
	Success bool `json:"success"`
}
type GetReq struct {
	Id string `json:"id"`
}
type GetUrlReq struct {
	Id string `url:"id"`
}
type GetRes struct {
	Count   int  `json:"Count"`
	Success bool `json:"success"`
}

func (t *httpTransport) Add(w http.ResponseWriter, r *http.Request) {
	w.Header().Set("Content-Type", "application/json")
	t.AddCounter()
	w.WriteHeader(http.StatusOK)
	json.NewEncoder(w).Encode(AddRes{Success: true})
}
func (t *httpTransport) Get(w http.ResponseWriter, r *http.Request) {
	var req GetReq
	json.NewDecoder(r.Body).Decode(&req)
	count := t.GetCounter(req.Id)
	json.NewEncoder(w).Encode(GetRes{Count: count, Success: true})
}
func (t *httpTransport) GetUrl(w http.ResponseWriter, r *http.Request) {
	u, err := url.ParseQuery(r.URL.RawQuery)
	if err != nil {
		json.NewEncoder(w).Encode(GetRes{Count: -1, Success: false})
	}
	id := u["id"][0]
	count := t.GetCounter(id)
	json.NewEncoder(w).Encode(GetRes{Count: count, Success: true})
}

// ---------- Domain -----------
type Counter struct {
	Count int
}

// ---------- Service -----------
type Service interface {
	AddCounter()
	GetCounter(id string) int
}
type service struct {
	Counter Counter
}

func NewService(c Counter) *service {
	return &service{Counter: c}
}
func (s *service) AddCounter() {
	s.Counter.Count++
}
func (s *service) GetCounter(id string) int {
	fmt.Println(id)
	return s.Counter.Count
}

// ---------- Main -----------
func main() {
	// Service
	s := NewService(Counter{})
	// Trnasport
	t := NewHttpTransport(s)
	// Routing
	r := mux.NewRouter()
	r.HandleFunc("/api/add", t.Add).Methods("POST")
	r.HandleFunc("/api/get", t.Get).Methods("GET")
	r.HandleFunc("/api/geturl", t.GetUrl).Methods("GET")
	// Server
	http.ListenAndServe(":3000", r)
}
```

## UNIT TEST

```go
package myUtils

import (
	"fmt"
)

func InnerF(s string) string {
	return fmt.Sprintf("I am the Utils function %s", s)
}

```
```go
package svc

import (
	"fmt"
	myUtils "tests/muUtils"
)

func MyService(s string) string {
	fmt.Println("I am the Service")
	out := myUtils.InnerF(s)
	fmt.Println("Service is Done")
	return out
}
```
### Closure for Mocking:-
```go
package myUtils

import (
	"fmt"
)

var InnerF = func(s string) string {
	return fmt.Sprintf("I am the Utils function %s", s)
}
```
```go

package tests

import (
	"fmt"
	"testing"
	myUtils "tests/muUtils"
	"tests/svc"
)

type Test struct {
	name     string
	data     string
	expected string
	err      error
}

func TestMyService(t *testing.T) {
	tests := []Test{{name: "Test-1", data: "My test 1", expected: "My test 1", err: nil}, {name: "Test-2", data: "My test 2", expected: "My test 2", err: nil}, {name: "Test-3", data: "My test 3", expected: "My test 3", err: nil}}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			myUtils.InnerF = func(s string) string {
				fmt.Println("MOCKED CALLING")
				return fmt.Sprintf("I am the MOCKED function %s", s)
			}
			out := svc.MyService(tt.data)
			fmt.Println("-------", out)
		})
	}
}
```
### Refactor for Dependency Injection

The Go testing framework doesn't have built-in mocking, but you can design your code to allow mocking by using interfaces and dependency injection.

```go
package svc

import (
	"fmt"
)

type MyService struct {
	InnerF func(s string) string
}

func NewService(innerF func(s string) string) *MyService {
	return &MyService{
		InnerF: innerF,
	}
}
func (m *MyService) MyService(s string) string {
	fmt.Println("I am the Service")
	out := m.InnerF(s)
	fmt.Println("Service is Done")
	return out
}
```
```go
package main

package tests

import (
	"fmt"
	"testing"
	"tests/svc"
)

func TestMyService(t *testing.T) {
	mockInnerF := func(s string) string {
		return "Mocked By me " + s
	}
	// mockInnerF = myUtils.InnerF

	t.Run("Mocked Test", func(t *testing.T) {
		s := svc.NewService(mockInnerF)
		out := s.MyService("Goal")
		fmt.Println(out)
	})
}

```

### Testify Mocking

Testify is a popular testing toolkit for Go. You can use its mock functionality to mock the function behavior directly within the test, even though it’s not ideal without dependency injection.
```go
package svc

import (
	"fmt"
)

type InnerFContract interface {
	InnerF(s string) string
}

func MyService(s string, innerF InnerFContract) string {
	fmt.Println("I am the Service")
	out := innerF.InnerF(s)
	fmt.Println("Service is Done")
	return out
}

```

```go
package tests

import (
	"fmt"
	"testing"
	"tests/svc"

	"github.com/stretchr/testify/mock"
)

type MockUtils struct {
	mock.Mock
}

func (mock MockUtils) InnerF(s string) string {
	args := mock.Called(s)
	lop := args.String(0)
	return lop
}

func TestMyService(t *testing.T) {
	mu := new(MockUtils)
	mu.On("InnerF", "Goal").Return("MMMMMocked by Me")
	out := svc.MyService("Goal", mu)
	fmt.Println(out)
}

```

Mocking Example:-

```go
package domain

type BaseEvent interface {
	GetRequestID() string
}

type EventContracts interface {
	List(eventIDs []string) error
	Get(event BaseEvent) error
}

```

```go
package utils

type BaseE struct {
}

func NewBaseEvenets() *BaseE {
	return &BaseE{}
}
func (b *BaseE) GetRequestID() string {
	return "Utils.BaseE.GetRequestID"
}

```

```go
package package1

import (
	"context"
	"errors"
	"fmt"
	"test/domain"
	"test/utils"
)

type MyService struct {
	Broker domain.EventContracts
	User   string
}

func (svc *MyService) GetBroker() (domain.EventContracts, error) {
	if svc.Broker != nil {
		return svc.Broker, nil
	}
	return nil, errors.New("broker Not Found")
}

func (svc *MyService) Publish(ctx context.Context, status int) error {
	if svc.Broker == nil {
		return errors.New("broker Not Found")
	}
	ctxReqID, _ := ctx.Value(0).(string)
	fmt.Println(ctxReqID)
	out := utils.NewBaseEvenets()
	err := svc.Broker.Get(out)
	return err
}

```

```go
package tests

import (
	"context"
	"fmt"
	"test/domain"
	"test/package1"
	"testing"

	"github.com/stretchr/testify/assert"
	"github.com/stretchr/testify/mock"
)

type MockBaseEvent struct {
	mock.Mock
}

func (mock *MockBaseEvent) Get(event domain.BaseEvent) error {
	fmt.Println("MockBaseEvent Get Called")
	args := mock.Called(event)
	out := args.Error(0)
	fmt.Println("out", out)
	return out
}
func (mock *MockBaseEvent) List(eventIDs []string) error {
	fmt.Println("MockBaseEvent List Called")
	args := mock.Called(eventIDs)
	out := args.Error(0)
	fmt.Println("out", out)
	return out
}
func TestMyServiceGetBroker(t *testing.T) {
	t.Run("No Error Get Broker", func(t *testing.T) {
		mockBaseEvent := new(MockBaseEvent)
		svc := package1.MyService{
			Broker: mockBaseEvent,
		}
		outGetBroker, err := svc.GetBroker()
		assert.NoError(t, err)
		fmt.Println("----", mockBaseEvent, "-:-", outGetBroker)
		assert.Equal(t, mockBaseEvent, outGetBroker)
	})
	t.Run("Error Get Broker", func(t *testing.T) {
		svc := package1.MyService{
			Broker: nil,
		}
		outGetBroker, err := svc.GetBroker()
		fmt.Println("----", outGetBroker)
		assert.Error(t, err)
		assert.Nil(t, outGetBroker)
	})
}

func TestPublish(t *testing.T) {
	t.Run("No Error Publish", func(t *testing.T) {
		mockBaseEvent := new(MockBaseEvent)
		svc := package1.MyService{
			Broker: mockBaseEvent,
		}
		ctx := context.WithValue(context.Background(), 0, "my Mocked ID")
		mockBaseEvent.On("Get", mock.Anything).Return(nil)
		err := svc.Publish(ctx, 0)
		assert.NoError(t, err)
	})
	t.Run("Error Publish", func(t *testing.T) {
		svc := package1.MyService{
			Broker: nil,
		}
		ctx := context.WithValue(context.Background(), 0, "my Mocked ID")
		err := svc.Publish(ctx, 0)
		assert.Error(t, err)
	})
}

```


## RabbitMQ Simplified Overview

## 1. What is RabbitMQ?
RabbitMQ is a tool that helps different programs (called "producers" and "consumers") talk to each other by sending messages. It acts as a middleman that stores messages and makes sure they are delivered reliably.

## 2. Core Concepts
- **Producer**: A program that sends messages to RabbitMQ.
- **Consumer**: A program that receives messages from RabbitMQ.
- **Queue**: A line where messages wait until they're picked up by consumers.
- **Exchange**: Decides where to send messages (to a queue) based on certain rules.
- **Binding**: The connection between a queue and an exchange.
- **Routing Key**: A label used by exchanges to decide how to send messages to queues.
- **Virtual Hosts (vhosts)**: Like separate workspaces in RabbitMQ, keeping things organized.
- **Message**: The actual data that is sent between producers and consumers.

## 3. Types of Exchanges
- **Direct Exchange**: Sends messages to a specific queue based on a routing key.
- **Fanout Exchange**: Sends messages to all queues it is connected to, no matter the routing key.
- **Topic Exchange**: Sends messages to queues based on matching parts of the routing key.
- **Headers Exchange**: Routes messages based on headers (metadata) rather than routing keys.

## 4. Types of Queues
- **Durable Queue**: Survives server restarts, messages are not lost.
- **Transient Queue**: Does not survive a server restart.
- **Exclusive Queue**: Only used by one consumer and deleted when that consumer disconnects.
- **Auto-Delete Queue**: Deletes itself when no consumers are using it.

## 5. Message Acknowledgement
- **Manual Acknowledgement**: The consumer confirms it has received and processed a message.
- **Automatic Acknowledgement**: The message is automatically confirmed once sent to the consumer.
- **Negative Acknowledgement (Nack)**: When a message is rejected by a consumer, RabbitMQ can retry or discard it.
- **Dead Letter Queues**: Special queues for storing messages that can't be delivered (e.g., after too many retries).

## 6. Producers and Consumers
- **Producer**: The program that sends messages to RabbitMQ.
- **Consumer**: The program that receives messages and processes them.

## 7. Reliability and Durability
- **Durable Queues and Exchanges**: Make sure queues and exchanges persist even after RabbitMQ restarts.
- **Persistent Messages**: Ensures that messages are saved to disk, not lost if RabbitMQ crashes.
- **Publisher Confirms**: A feature where RabbitMQ confirms that a message was successfully received.
- **Transactions**: A way for producers to send messages in a safe, all-or-nothing manner, but not very efficient.

## 8. Routing Messages
- **Routing Key**: A unique label for each message, used to route it to the correct queue.
- **Binding**: A connection between an exchange and a queue, based on the routing key.
- **Topic-based Routing**: Allows complex routing of messages using patterns in the routing key.

## 9. Common Messaging Patterns
- **Work Queues**: Distribute tasks evenly across many consumers (load balancing).
- **Publish/Subscribe**: Send the same message to many consumers at once (fanout exchange).
- **Request/Response**: One program sends a message and waits for a response (like calling an API).
- **Routing**: Direct messages to specific queues based on the routing key (direct exchange).
- **Topics**: Route messages based on patterns (like sending messages with “sports.*” to a sports-related queue).

## 10. Clustering and High Availability
- **Clustering**: Running RabbitMQ on multiple servers to spread the load and ensure it's always available.
- **Mirrored Queues**: Copying queues to other servers in the cluster so that if one server fails, messages are still available.
- **Federation**: Allowing RabbitMQ instances in different locations to share messages.

## 11. Management and Monitoring
- **RabbitMQ Management Plugin**: A web-based interface to monitor and control RabbitMQ.
- **Metrics**: Information about queues, exchanges, and message flow to help track performance.
- **Logging**: Tracks errors and important events for troubleshooting.

## 12. Security
- **Authentication**: Verifying who is connecting to RabbitMQ (like a username and password).
- **Authorization**: Controlling what different users can do (like which queues they can read from).
- **SSL/TLS**: Secure communication between RabbitMQ and clients using encryption.
- **Access Control Lists (ACLs)**: Fine-grained control over who can access RabbitMQ resources.

## 13. Performance Tuning
- **Prefetch Count**: Limit how many messages a consumer can process at once to avoid overwhelming it.
- **Concurrency**: Running many consumers in parallel to handle more messages.
- **Flow Control**: Managing how messages are processed to avoid overloading RabbitMQ.
- **Memory and Disk Usage**: Keep track of resource usage to prevent performance issues.

## 14. Integration with Other Technologies
- **Client Libraries**: RabbitMQ supports many programming languages (like Java, Python, and .NET) for sending and receiving messages.
- **REST API**: Provides an HTTP interface to manage RabbitMQ resources programmatically.
- **Microservices**: RabbitMQ is often used for communication between services in a microservice architecture.

## 15. Common Use Cases
- **Event-Driven Architecture**: Services communicate by sending and reacting to events (e.g., new user sign-up).
- **Task Queues**: Distributing tasks among multiple workers (e.g., image processing).
- **Distributed Systems**: Ensuring different parts of a system can communicate asynchronously.
- **Log Aggregation**: Collecting logs from various services into a central system.

## 16. Best Practices
- **Avoid Blocking**: Ensure consumers handle messages quickly to prevent delays.
- **Use Dead Letter Queues**: Handle failed messages properly instead of letting them get lost.
- **Monitor RabbitMQ**: Keep an eye on performance metrics to spot problems early.
- **Backpressure**: Apply limits to prevent RabbitMQ from getting overwhelmed.

## 17. Troubleshooting and Debugging
- **Connection Issues**: Use logs or the management UI to check for connection problems.
- **Queue Lengths**: If queues grow too long, it can indicate that consumers aren’t keeping up.
- **Dead-letter Queues**: Check for messages that couldn’t be delivered and figure out why.
- **Resource Usage**: Make sure RabbitMQ has enough memory and disk space to perform well.

## 18. Advanced Topics
- **Shovel Plugin**: Move messages between different RabbitMQ servers or clusters.
- **High Availability Queues**: Set up replicated queues for failover and redundancy.
- **Quorum Queues**: A newer type of queue that offers better reliability and replication.
- **Distributed Tracing**: Track the flow of messages across different services for debugging.

```go
package main

import (
	"fmt"
	"log"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:guest@localhost:5672/")
	if err != nil {
		log.Fatalf("Failed to connect: %s", err)
	}
	defer conn.Close()

	ch, err := conn.Channel()
	if err != nil {
		log.Fatalf("Failed to create channel: %s", err)
	}
	defer ch.Close()

	exchange := "topic_logs"
	ch.ExchangeDeclare(exchange, "topic", true, false, false, false, nil)

	routingKey := "animal.cat"
	message := "Topic exchange message for cat"
	err = ch.Publish(exchange, routingKey, false, false, amqp.Publishing{
		ContentType: "text/plain",
		Body:        []byte(message),
	})
	if err != nil {
		log.Fatalf("Failed to send message: %s", err)
	}
	fmt.Println("Message sent:", message)
}

```

```go
package main

import (
	"fmt"
	"log"

	"github.com/streadway/amqp"
)

func main() {
	conn, err := amqp.Dial("amqp://guest:guest@localhost:5672/")
	if err != nil {
		log.Fatalf("Failed to connect: %s", err)
	}
	defer conn.Close()

	ch, err := conn.Channel()
	if err != nil {
		log.Fatalf("Failed to create channel: %s", err)
	}
	defer ch.Close()

	exchange := "topic_logs"
	ch.ExchangeDeclare(exchange, "topic", true, false, false, false, nil)

	queue, err := ch.QueueDeclare("", false, true, true, false, nil)
	if err != nil {
		log.Fatalf("Failed to declare queue: %s", err)
	}

	routingKey := "animal.*"
	err = ch.QueueBind(queue.Name, routingKey, exchange, false, nil)
	if err != nil {
		log.Fatalf("Failed to bind queue: %s", err)
	}

	msgs, err := ch.Consume(queue.Name, "", true, false, false, false, nil)
	if err != nil {
		log.Fatalf("Failed to consume messages: %s", err)
	}

	for msg := range msgs {
		fmt.Printf("Received message: %s\n", msg.Body)
	}
}

```
Direct Exchange
In Direct Exchange, messages are routed to queues based on an exact match between the routing key in the message and the binding key in the queue.

Publisher (Direct Exchange):
Exchange Type: "direct"
Routing Key: The publisher sends a message with a specific routing key (e.g., "error"), which is used to determine which queues receive the message.
```go
// Declare direct exchange
ch.ExchangeDeclare("direct_logs", "direct", true, false, false, false, nil)

// Publish a message with a routing key (e.g., "error")
err = ch.Publish("direct_logs", "error", false, false, amqp.Publishing{
    ContentType: "text/plain",
    Body:        []byte("Error in processing the task"),
})
```
Subscriber (Direct Exchange):
Binding Key: The subscriber binds a queue to the exchange using the binding key ("error") so it will receive messages published with the same routing key.
```go
// Declare direct exchange
ch.ExchangeDeclare("direct_logs", "direct", true, false, false, false, nil)

// Declare an anonymous queue
queue, err := ch.QueueDeclare("", false, true, true, false, nil)
if err != nil {
    log.Fatalf("Failed to declare queue: %s", err)
}

// Bind the queue to the exchange with the routing key "error"
err = ch.QueueBind(queue.Name, "error", "direct_logs", false, nil)
```
In Fanout Exchange, the message is sent to all queues bound to the exchange, regardless of any routing keys. It broadcasts the message to all subscribers.

Publisher (Fanout Exchange):
Exchange Type: "fanout"
Routing Key: The routing key is ignored in this case. The message is simply sent to all bound queues.
```go
// Declare fanout exchange
ch.ExchangeDeclare("logs", "fanout", true, false, false, false, nil)

// Publish a message (routing key is ignored for fanout)
err = ch.Publish("logs", "", false, false, amqp.Publishing{
    ContentType: "text/plain",
    Body:        []byte("Fanout exchange message"),
})
```
Subscriber (Fanout Exchange):
Binding Key: Not needed because all queues bound to the fanout exchange receive the message. The subscriber simply binds to the exchange without a specific routing key.
```go
// Declare fanout exchange
ch.ExchangeDeclare("logs", "fanout", true, false, false, false, nil)

// Declare an anonymous queue
queue, err := ch.QueueDeclare("", false, true, true, false, nil)
if err != nil {
    log.Fatalf("Failed to declare queue: %s", err)
}

// Bind the queue to the exchange (routing key is ignored for fanout)
err = ch.QueueBind(queue.Name, "", "logs", false, nil)
```
 Topic Exchange
In Topic Exchange, messages are routed to queues based on the routing pattern specified by the routing key. The routing key can include wildcards (* for a single word and # for multiple words).

Publisher (Topic Exchange):
Exchange Type: "topic"
Routing Key: The routing key can include wildcard patterns to route messages to multiple queues that match the pattern.
```go
// Declare topic exchange
ch.ExchangeDeclare("topic_logs", "topic", true, false, false, false, nil)

// Publish a message with a routing key (e.g., "animal.cat")
err = ch.Publish("topic_logs", "animal.cat", false, false, amqp.Publishing{
    ContentType: "text/plain",
    Body:        []byte("Topic exchange message for cat"),
})
```
Subscriber (Topic Exchange):
Binding Key: The subscriber binds to the exchange with a pattern-based routing key (e.g., animal.*), which means it will receive messages with routing keys like animal.cat, animal.dog, etc.
```go
// Declare topic exchange
ch.ExchangeDeclare("topic_logs", "topic", true, false, false, false, nil)

// Declare an anonymous queue
queue, err := ch.QueueDeclare("", false, true, true, false, nil)
if err != nil {
    log.Fatalf("Failed to declare queue: %s", err)
}

// Bind the queue to the exchange with a wildcard routing key (e.g., "animal.*")
err = ch.QueueBind(queue.Name, "animal.*", "topic_logs", false, nil)
```
 Headers Exchange
In Header Exchange, messages are routed based on message headers rather than the routing key. The routing decisions are made based on specific header values (e.g., X-Color: Red).

Publisher (Header Exchange):
Exchange Type: "headers"
Headers: The publisher sends the message with header attributes (e.g., X-Color: Red)
```go
// Declare header exchange
ch.ExchangeDeclare("header_logs", "headers", true, false, false, false, nil)

// Define message headers
headers := amqp.Table{
    "X-Color": "Red",  // Set a header key-value pair
}

// Publish a message with headers
err = ch.Publish("header_logs", "", false, false, amqp.Publishing{
    Headers:     headers,
    ContentType: "text/plain",
    Body:        []byte("Header exchange message with color Red"),
})
```
Subscriber (Header Exchange):
Binding Key: Instead of a routing key, the subscriber binds the queue using header matching. In this case, the subscriber binds the queue to the exchange with specific headers (e.g., X-Color: Red).

```go
// Declare header exchange
ch.ExchangeDeclare("header_logs", "headers", true, false, false, false, nil)

// Declare an anonymous queue
queue, err := ch.QueueDeclare("", false, true, true, false, nil)
if err != nil {
    log.Fatalf("Failed to declare queue: %s", err)
}

// Bind the queue using header matching (e.g., "X-Color" equals "Red")
headers := amqp.Table{
    "X-Color": "Red",  // Match this header for routing
}
err = ch.QueueBind(queue.Name, "", "header_logs", false, headers)

```
