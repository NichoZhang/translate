# Microservices in Go
我最近参加了在 Melbourne 举办的 Golang 线下讨论会，会议讨论了如何开发微服务和框架。在这篇文章里，我将与你们分享我知道的内容。

如下这些框架是我将要对比的内容：
[Go Micro](https://micro.mu/)
[Go Kit](https://gokit.io/)
[Gizmo](https://github.com/NYTimes/gizmo)
[Kite](https://github.com/koding/kite)

## 框架介绍
### Go Micro

在我认为，这是最流行的框架之一。在网上可以找到大量的文章和简单实例。你也可以在Medium上关注 [microhq](https://medium.com/microhq) ，或者在 twitter上关注 [@MicroHQ](https://twitter.com/MicroHQ) 来获取最新的消息。

那么，究竟什么是Go Micro？它是由Go语言编写的一种可插拔式的RPC微服务框架。去掉外壳，你可以看到：

* 服务发现 - 应用程序通过服务发现功能进行自动注册；
* 负载均衡 - 客户端侧的负载均衡用于分散接收来自不同服务端的请求；
* 同步通信 - 消息的请求/响应由传输层来提供；
* 异步通信 - 提供构建发布/订阅功能；
* 消息编码 - 依据请求头部中/Content-type/的要求进行编码/解码；
* RPC客户端/服务端 - 利用上述功能并提供界面话构建微服务。

Go Micro 架构能描述成如下三层。

[image:ED501702-05C1-4DFD-91C1-A0C606A79827-994-0004451F067693CF/CE205DC8-4993-4D2D-9661-92EA60F38705.png]
                                          图 1. Go Micro architecture

最顶层包含了一个客户端-服务端模式和一个抽象的服务。*服务端*是一个，*客户端*提供一个可以用于请求到服务端的接口。

在架构图的底层包含如下几种插件：

* 交易员 - 提供一个让交易员使用的异步发布/订阅通信的接口；
* 代码编译器 - 用于处理编码/解码。支持的格式包括 json，bson，protobuf，msgpack等等；
* 注册器 - 提供一个服务发现机制；
* 选择器 - 创建在注册器上的负载均衡抽象概念。它允许服务被通过类似一些算法被“选中”，例如随机算法，roundrobin，leastconn等等；
* 交互 - 连接在不同服务中的请求/响应同步通信接口。

Go Micro同样也提供/车斗/（Sidecar）这种特性。这种特性使得你可以不只用Go语言来编写服务。车斗提供服务注册功能，gRPC 编码/解码和 HTTP处理。Go Micro可以为多种语言提供服务。

### Go Kit
Go Kit 是一组使用Go语言编写的工具包。并不太像Go Micro，Go Kit的公共包被设计成必须要引入二进制文件。

Go Kit有如下几条规则：

* 没有全局状态；
* 定义了结构；
* 强依赖性；
* 如果合约一样的接口；
* 领域驱动设计（DDD）。

在Go Kit中你可以发现如下包：

* 认证 - 基础的和JWT；
* 交互 - HTTP，Nats，gRPC和其他；
* 日志 - 通用日志服务接口；
* 监控 - CloudWatch，Statsd，Graphite和其他的；
* 跟踪 - Zipkin 和 Opentracing；
* 服务发现 - Consul，Etcd，Eureka和其他的；
* 断路器 - 使用Go实现的Hystrix。

关于Go Kit，其中一个最好的描述，Peter Bourgon的文章和附件PPT中说道：

* [Peter Bourgon · Go kit: Go in the modern enterprise](https://peter.bourgon.org/go-kit/)

* [GitHub - peterbourgon/go-microservices: Go microservices workshop example project](https://github.com/peterbourgon/go-microservices)

同样，在“Go + Microservices”的PPT中你可以找到通过Go Kit构建服务的例子。为了快速了解，下图是服务的一个架构：

                            [image:E85F7E88-4C96-496C-A4B1-337C77DE4080-994-000457689D77F388/D327D834-BBA7-4651-9973-E5E06982A734.png]
   图 2. Go Kit的架构图 (原图在 “Go + microservices” 的幻灯片里)

### Gizmo

Gizmo 是纽约时报使用的微服务套件。它提供了后台进行订阅发布和聚集服务的包。它提供了如下包：

* 服务 - 提供两个服务实现：SimpleServer，RPCServer；
* 服务/组件 - 基于Go Kit实现的实验性组件；
* 配置 - 包含的方法可以通过 JSON，JSON blobs in Consul K/V 或者环境变量等方式进行配置；
* 发布/订阅 - 提供标准的发布接口和从队列中消费数据的接口；
* 发布订阅/发布订阅套装 - 包含了发布者和订阅者的接口实现；
* 网站 - 提供从请求查询解析和负载均衡的方法。

发布/订阅包支持如下消息队列的接口：
* [pubsub//aws](https://godoc.org/github.com/NYTimes/gizmo/pubsub/aws) - for Amazon SNS//SQS.
* [pubsub//gcp](https://godoc.org/github.com/NYTimes/gizmo/pubsub/gcp) - for Google Pubsub.
* [pubsub//kafka](https://godoc.org/github.com/NYTimes/gizmo/pubsub/kafka) - for kafka topics.
* [pubsub//http](https://godoc.org/github.com/NYTimes/gizmo/pubsub/http) - for publishing via HTTP.

所以，在我的观点来看，Gizmo套装是介于Go Micro 和 Go Kit 之间的。它不想 Go Micro 一样是一个完整的“黑盒子”。同样的，它又不想是 Go Kit 那么的原生。它提供了更高级的组件构造方式，就像其提供的配置文件包和发布订阅包一样。

### Kite
Kite是一个用Go语言开发中的微服务框架。它提供了RPC客户端和服务端包。创建服务并且通过Kontrol系统可以自动注册服务。Kontrol是利用Kite编写的，它就是Kite自服务的一种方式。这就好象是Kite微服务的框架运行在自己的环境设定中。如果你需要使用Kite微服务框架连接发现其他系统，它需要定制访问请求。这也就是我从列表中删掉Kite框架的主要原因，并且决定不会在回顾这个框架了。

## 框架对比
下面，我将会使用四个角度来对比之前说到的框架：
* 客观评价 - 基于 GitHub 上的统计
* 文档和示例
* 用户和社群
* 代码质量

### 基于GitHub上的统计
[image:BFF7E701-5645-4B8D-A85F-3F9AA699C226-3232-00021CC946146E30/FE9D08BF-0071-41E1-85F5-ADC482F86F77.png]
                       表 1. Go 微服务框架统计（统计于2018年4月）

### 文档和示例
嗯，简单来说，上面提到的框架都没有提供可靠的文档。
一般来说，他们就提供了一个 readme 在repo的首页。

对于Go Micro的大部分信息和公告都在 micro.mu，microhq和media的@MicroHQ上。

而Go Kit的最好介绍都在Peter Bourgon’s blog上，我能找到的最好示例都在ru-rocker blog上。

对于Gizmo，源代码就是最好的文档和示例了。

概括来说，如果你是从NodeJS世界过来的，并且期望看到和ExpressJS一样的指导说明，那你肯定要失望了。另一方面，现在创建一个来自于你的优秀指导方案是一个好机会。

### 用户和社群
Go Kit是最流行的微服务框架了，从GitHub的统计来看，它目前已经超过1万颗星。它还有大量的开源开发人员（122人）并且超过了1000次复制。最后，DigitalOcean已经可以支持Go Kit了。

拥有3600颗星，27个贡献者并且被复制了385次的Go Micro排在了第二名。最支持Go Micro的是Sixt平台。

随后排在第三的是Gizmo。超过了2200颗星，31个贡献者和被复制了137次。最支持的平台是New York Times，也就是它的发明者们。

### 代码质量
Go Kit的代码质量是最好的。它的代码基本覆盖了80%的Go report rating。Gizmo同样严格执行了Go report rating。但是它的覆盖率仅有46%。Go Mirco没提供代码覆盖信息，但是它同样很好的执行了Go report rating。

## 编写微服务
好了，之前都是理论。为了更好的理解框架，我将用三个例子来说明微服务。
[image:B4BCFD71-5164-4666-9F91-8867E882AB52-3232-00021DE8A0608369/3462065E-8CE4-40E4-8E5D-1D083AEC6575.png]
                                 图 3. 真实的微服务框架示例

这些服务实现了一个商业案例 - 欢迎页。
当一个用户发送了一个「名字」给服务端，服务端返回一个“欢迎”的响应。
所以，所有的服务都应该满足如下的要求：

* 服务应该可以通过服务发现系统进行自己注册。
* 服务应该能够在后端进行健康检查。
* 服务应该支持简单的HTTP和gRPC通信。

对于以上这些，你最好去阅读下源码。你可以暂停继续读下去，然后去代码仓库看下[源码](https://github.com/antklim/go-microservices)。

### Go Micro greeter
第一步，你可以使用Go Micro创建一个服务，并定义一个protobuf的描述文件。接下来，将这个protobuf描述文件放到所有的服务中。我用在服务中的protobuf描述如下：

``` protobuf
syntax = "proto3";

package pb;

service Greeter {
  rpc Greeting(GreetingRequest) returns (GreetingResponse) {}
}

message GreetingRequest {
  string name = 1;
}

message GreetingResponse {
  string greeting = 2;
}
```
接口中包含一个方法 - ‘Greeting’。请求中有一个参数 - ‘name’，在响应中有一个参数 - ‘greeting’。

我使用[protoc](https://github.com/micro/protoc-gen-micro)工具来生成一个用protobuf的服务接口。这个生成器是Go Micro提供的，并且支持了框架中的一些特性。我将这些所有的服务通过’greeter’连接起来。然后，服务发现系统开始发现并注册服务。它仅支持gRPC传输协议：

``` go
package main

import (
	"log"

	pb "github.com/antklim/go-microservices/go-micro-greeter/pb"
	"github.com/micro/go-micro"
	"golang.org/x/net/context"
)

// Greeter implements greeter service.
type Greeter struct{}

// Greeting method implementation.
func (g *Greeter) Greeting(ctx context.Context, in *pb.GreetingRequest, out *pb.GreetingResponse) error {
	out.Greeting = "GO-MICRO Hello " + in.Name
	return nil
}

func main() {
	service := micro.NewService(
		micro.Name("go-micro-srv-greeter"),
		micro.Version("latest"),
	)

	service.Init()

	pb.RegisterGreeterHandler(service.Server(), new(Greeter))

	if err := service.Run(); err != nil {
		log.Fatal(err)
	}
}
```

为了支持HTTP传输，我不得不添加其他模块。它映射了HTTP的请求到protobuf定义的请求格式。并称为gRPC服务。然后映射了服务的响应结果到HTTP响应结果和对用户的应答。

``` go
package main

import (
	"context"
	"encoding/json"
	"log"
	"net/http"

	proto "github.com/antklim/go-microservices/go-micro-greeter/pb"
	"github.com/micro/go-micro/client"
	web "github.com/micro/go-web"
)

func main() {
	service := web.NewService(
		web.Name("go-micro-web-greeter"),
	)

	service.HandleFunc("/greeting", func(w http.ResponseWriter, r *http.Request) {
		if r.Method == "GET" {
			var name string
			vars := r.URL.Query()
			names, exists := vars["name"]
			if !exists || len(names) != 1 {
				name = ""
			} else {
				name = names[0]
			}

			cl := proto.NewGreeterClient("go-micro-srv-greeter", client.DefaultClient)
			rsp, err := cl.Greeting(context.Background(), &proto.GreetingRequest{Name: name})
			if err != nil {
				http.Error(w, err.Error(), 500)
				return
			}

			js, err := json.Marshal(rsp)
			if err != nil {
				http.Error(w, err.Error(), http.StatusInternalServerError)
				return
			}

			w.Header().Set("Content-Type", "application/json")
			w.Write(js)
			return
		}
	})

	if err := service.Init(); err != nil {
		log.Fatal(err)
	}

	if err := service.Run(); err != nil {
		log.Fatal(err)
	}
}
```

简单直接。许多情况都需要通过Go Micro之后才可以实现，例如，注册到服务发现系统。另一方面，非常写一个纯HTTP服务也是很难的。

### Go Kit greeter
刚刚我完成了Go Micro部分，我们现在使用Go Kit来实现一些服务。我曾花了大量时间来阅读Go Kit代码库中提供的代码示例。理解后端的概念也花费了我一些时间。另一个消耗时间的问题是服务发现注册代码。在我发现一个好的[示例](http://www.ru-rocker.com/2017/04/17/micro-services-using-go-kit-service-discovery/)之前，我都没有实现它。

最后，我写了四个包：
* 服务逻辑实现。
* 不确定后端通信服务。
* 指定后端通信服务（gRPC，HTTP）。
* 服务发现注册。

```go
package greeterservice

// Service describe greetings service.
type Service interface {
	Health() bool
	Greeting(name string) string
}

// GreeterService implementation of the Service interface.
type GreeterService struct{}

// Health implementation of the Service.
func (GreeterService) Health() bool {
	return true
}

// Greeting implementation of the Service.
func (GreeterService) Greeting(name string) (greeting string) {
	greeting = "GO-KIT Hello " + name
	return
}
```

如你所见，代码没有任何依赖。它仅仅是实现逻辑。下一段代码实现了后端定义：
``` go
package greeterendpoint

import (
	"context"

	"github.com/go-kit/kit/log"

	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greeterservice"
	"github.com/go-kit/kit/endpoint"
)

// Endpoints 收集所有的后端组成greeter服务。
// 它是用helper struct收集所有的后端到一个单独的参数里。
type Endpoints struct {
	HealthEndpoint   endpoint.Endpoint // used by Consul for the healthcheck
	GreetingEndpoint endpoint.Endpoint
}

// MakeServerEndpoints 返回服务后端，用所有提供的中间件连接起来。
func MakeServerEndpoints(s greeterservice.Service, logger log.Logger) Endpoints {
	var healthEndpoint endpoint.Endpoint
	{
		healthEndpoint = MakeHealthEndpoint(s)
		healthEndpoint = LoggingMiddleware(log.With(logger, "method", "Health"))(healthEndpoint)
	}

	var greetingEndpoint endpoint.Endpoint
	{
		greetingEndpoint = MakeGreetingEndpoint(s)
		greetingEndpoint = LoggingMiddleware(log.With(logger, "method", "Greeting"))(greetingEndpoint)
	}

	return Endpoints{
		HealthEndpoint:   healthEndpoint,
		GreetingEndpoint: greetingEndpoint,
	}
}

// MakeHealthEndpoint 构建一个带health的后端把服务包起来。
func MakeHealthEndpoint(s greeterservice.Service) endpoint.Endpoint {
	return func(ctx context.Context, request interface{}) (response interface{}, err error) {
		healthy := s.Health()
		return HealthResponse{Healthy: healthy}, nil
	}
}

// MakeGreetingEndpoint 构建一个Greeter后端把服务包起来。
func MakeGreetingEndpoint(s greeterservice.Service) endpoint.Endpoint {
	return func(ctx context.Context, request interface{}) (response interface{}, err error) {
		req := request.(GreetingRequest)
		greeting := s.Greeting(req.Name)
		return GreetingResponse{Greeting: greeting}, nil
	}
}

// Failer is an interface that should be implemented by response types.
// Response encoders can check if responses are Failer, and if so if they've
// failed, and if so encode them using a separate write path based on the error.
type Failer interface {
	Failed() error
}

// HealthRequest collects the request parameters for the Health method.
type HealthRequest struct{}

// HealthResponse collects the response values for the Health method.
type HealthResponse struct {
	Healthy bool  `json:"healthy,omitempty"`
	Err     error `json:"err,omitempty"`
}

// Failed implements Failer.
func (r HealthResponse) Failed() error { return r.Err }

// GreetingRequest collects the request parameters for the Greeting method.
type GreetingRequest struct {
	Name string `json:"name,omitempty"`
}

// GreetingResponse collects the response values for the Greeting method.
type GreetingResponse struct {
	Greeting string `json:"greeting,omitempty"`
	Err      error  `json:"err,omitempty"`
}

// Failed implements Failer.
func (r GreetingResponse) Failed() error { return r.Err }
```

当定义好所有的后端，我将通过不同的通信协议提供接口。我先从HTTP通信开始：

``` go
package greetertransport

import (
	"context"
	"encoding/json"
	"errors"
	"net/http"

	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greeterendpoint"
	"github.com/go-kit/kit/log"
	httptransport "github.com/go-kit/kit/transport/http"
	"github.com/gorilla/mux"
)

var (
	// ErrBadRouting is returned when an expected path variable is missing.
	ErrBadRouting = errors.New("inconsistent mapping between route and handler")
)

// NewHTTPHandler returns an HTTP handler that makes a set of endpoints
// available on predefined paths.
func NewHTTPHandler(endpoints greeterendpoint.Endpoints, logger log.Logger) http.Handler {
	m := mux.NewRouter()
	options := []httptransport.ServerOption{
		httptransport.ServerErrorEncoder(encodeError),
		httptransport.ServerErrorLogger(logger),
	}

	// GET /health         retrieves service heath information
	// GET /greeting?name  retrieves greeting

	m.Methods("GET").Path("/health").Handler(httptransport.NewServer(
		endpoints.HealthEndpoint,
		DecodeHTTPHealthRequest,
		EncodeHTTPGenericResponse,
		options...,
	))
	m.Methods("GET").Path("/greeting").Handler(httptransport.NewServer(
		endpoints.GreetingEndpoint,
		DecodeHTTPGreetingRequest,
		EncodeHTTPGenericResponse,
		options...,
	))
	return m
}

// DecodeHTTPHealthRequest method.
func DecodeHTTPHealthRequest(_ context.Context, _ *http.Request) (interface{}, error) {
	return greeterendpoint.HealthRequest{}, nil
}

// DecodeHTTPGreetingRequest method.
func DecodeHTTPGreetingRequest(_ context.Context, r *http.Request) (interface{}, error) {
	vars := r.URL.Query()
	names, exists := vars["name"]
	if !exists || len(names) != 1 {
		return nil, ErrBadRouting
	}
	req := greeterendpoint.GreetingRequest{Name: names[0]}
	return req, nil
}

func encodeError(_ context.Context, err error, w http.ResponseWriter) {
	w.WriteHeader(err2code(err))
	json.NewEncoder(w).Encode(errorWrapper{Error: err.Error()})
}

func err2code(err error) int {
	switch err {
	default:
		return http.StatusInternalServerError
	}
}

type errorWrapper struct {
	Error string `json:"error"`
}

// EncodeHTTPGenericResponse is a transport/http.EncodeResponseFunc that encodes
// the response as JSON to the response writer
func EncodeHTTPGenericResponse(ctx context.Context, w http.ResponseWriter, response interface{}) error {
	if f, ok := response.(greeterendpoint.Failer); ok && f.Failed() != nil {
		encodeError(ctx, f.Failed(), w)
		return nil
	}
	w.Header().Set("Content-Type", "application/json; charset=utf-8")
	return json.NewEncoder(w).Encode(response)
}
```

在我开始实现gRPC后端代码之前，我不需要提供protobuf的定义。我复制了Go Micro服务的 protobuf。但是这是在 Go Kit框架里，我使用默认的服务生成器创建了服务接口。

``` shell
#!/usr/bin/env sh
protoc greeter.proto --go_out=plugins=grpc:.
```
``` go
package greetertransport

import (
	"context"

	"github.com/antklim/go-microservices/go-kit-greeter/pb"
	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greeterendpoint"
	"github.com/go-kit/kit/log"
	grpctransport "github.com/go-kit/kit/transport/grpc"
	oldcontext "golang.org/x/net/context"
)

type grpcServer struct {
	greeter grpctransport.Handler
}

// NewGRPCServer makes a set of endpoints available as a gRPC GreeterServer.
func NewGRPCServer(endpoints greeterendpoint.Endpoints, logger log.Logger) pb.GreeterServer {
	options := []grpctransport.ServerOption{
		grpctransport.ServerErrorLogger(logger),
	}

	return &grpcServer{
		greeter: grpctransport.NewServer(
			endpoints.GreetingEndpoint,
			decodeGRPCGreetingRequest,
			encodeGRPCGreetingResponse,
			options...,
		),
	}
}

// Greeting implementation of the method of the GreeterService interface.
func (s *grpcServer) Greeting(ctx oldcontext.Context, req *pb.GreetingRequest) (*pb.GreetingResponse, error) {
	_, res, err := s.greeter.ServeGRPC(ctx, req)
	if err != nil {
		return nil, err
	}
	return res.(*pb.GreetingResponse), nil
}

// decodeGRPCGreetingRequest is a transport/grpc.DecodeRequestFunc that converts
// a gRPC greeting request to a user-domain greeting request.
func decodeGRPCGreetingRequest(_ context.Context, grpcReq interface{}) (interface{}, error) {
	req := grpcReq.(*pb.GreetingRequest)
	return greeterendpoint.GreetingRequest{Name: req.Name}, nil
}

// encodeGRPCGreetingResponse is a transport/grpc.EncodeResponseFunc that converts
// a user-domain greeting response to a gRPC greeting response.
func encodeGRPCGreetingResponse(_ context.Context, response interface{}) (interface{}, error) {
	res := response.(greeterendpoint.GreetingResponse)
	return &pb.GreetingResponse{Greeting: res.Greeting}, nil
}
```
最后，我实现了服务发现注册：
``` go
package greetersd

import (
	"math/rand"
	"os"
	"strconv"
	"time"

	"github.com/go-kit/kit/log"
	"github.com/go-kit/kit/sd"
	consulsd "github.com/go-kit/kit/sd/consul"
	"github.com/hashicorp/consul/api"
)

// ConsulRegister method.
func ConsulRegister(consulAddress string,
	consulPort string,
	advertiseAddress string,
	advertisePort string) (registar sd.Registrar) {

	// Logging domain.
	var logger log.Logger
	{
		logger = log.NewLogfmtLogger(os.Stderr)
		logger = log.With(logger, "ts", log.DefaultTimestampUTC)
		logger = log.With(logger, "caller", log.DefaultCaller)
	}

	rand.Seed(time.Now().UTC().UnixNano())

	// Service discovery domain. In this example we use Consul.
	var client consulsd.Client
	{
		consulConfig := api.DefaultConfig()
		consulConfig.Address = consulAddress + ":" + consulPort
		consulClient, err := api.NewClient(consulConfig)
		if err != nil {
			logger.Log("err", err)
			os.Exit(1)
		}
		client = consulsd.NewClient(consulClient)
	}

	check := api.AgentServiceCheck{
		HTTP:     "http://" + advertiseAddress + ":" + advertisePort + "/health",
		Interval: "10s",
		Timeout:  "1s",
		Notes:    "Basic health checks",
	}

	port, _ := strconv.Atoi(advertisePort)
	num := rand.Intn(100) // to make service ID unique
	asr := api.AgentServiceRegistration{
		ID:      "go-kit-srv-greeter-" + strconv.Itoa(num), //unique service ID
		Name:    "go-kit-srv-greeter",
		Address: advertiseAddress,
		Port:    port,
		Tags:    []string{"go-kit", "greeter"},
		Check:   &check,
	}
	registar = consulsd.NewRegistrar(client, &asr, logger)
	return
}
```
之后我创建了预处理块，我将在服务开始的时候把它们都连在一起：
``` go
package main

import (
	"flag"
	"fmt"
	"net"
	"net/http"
	"os"
	"os/signal"
	"syscall"
	"text/tabwriter"

	"github.com/antklim/go-microservices/go-kit-greeter/pb"
	"google.golang.org/grpc"

	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greeterendpoint"
	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greetersd"
	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greeterservice"
	"github.com/antklim/go-microservices/go-kit-greeter/pkg/greetertransport"

	"github.com/go-kit/kit/log"
	"github.com/oklog/oklog/pkg/group"
)

func main() {
	fs := flag.NewFlagSet("greetersvc", flag.ExitOnError)
	var (
		debugAddr  = fs.String("debug.addr", ":9100", "Debug and metrics listen address")
		consulAddr = fs.String("consul.addr", "", "Consul Address")
		consulPort = fs.String("consul.port", "8500", "Consul Port")
		httpAddr   = fs.String("http.addr", "", "HTTP Listen Address")
		httpPort   = fs.String("http.port", "9110", "HTTP Listen Port")
		grpcAddr   = fs.String("grpc-addr", ":9120", "gRPC listen address")
	)
	fs.Usage = usageFor(fs, os.Args[0]+" [flags]")
	fs.Parse(os.Args[1:])

	var logger log.Logger
	{
		logger = log.NewLogfmtLogger(os.Stderr)
		logger = log.With(logger, "ts", log.DefaultTimestampUTC)
		logger = log.With(logger, "caller", log.DefaultCaller)
	}

	var service greeterservice.Service
	{
		service = greeterservice.GreeterService{}
		service = greeterservice.LoggingMiddleware(logger)(service)
	}

	var (
		endpoints   = greeterendpoint.MakeServerEndpoints(service, logger)
		httpHandler = greetertransport.NewHTTPHandler(endpoints, logger)
		registar    = greetersd.ConsulRegister(*consulAddr, *consulPort, *httpAddr, *httpPort)
		grpcServer  = greetertransport.NewGRPCServer(endpoints, logger)
	)

	var g group.Group
	{
		// The debug listener mounts the http.DefaultServeMux, and serves up
		// stuff like the Go debug and profiling routes, and so on.
		debugListener, err := net.Listen("tcp", *debugAddr)
		if err != nil {
			logger.Log("transport", "debug/HTTP", "during", "Listen", "err", err)
			os.Exit(1)
		}
		g.Add(func() error {
			logger.Log("transport", "debug/HTTP", "addr", *debugAddr)
			return http.Serve(debugListener, http.DefaultServeMux)
		}, func(error) {
			debugListener.Close()
		})
	}
	{
		// The service discovery registration.
		g.Add(func() error {
			logger.Log("transport", "HTTP", "addr", *httpAddr, "port", *httpPort)
			registar.Register()
			return http.ListenAndServe(":"+*httpPort, httpHandler)
		}, func(error) {
			registar.Deregister()
		})
	}
	{
		// The gRPC listener mounts the Go kit gRPC server we created.
		grpcListener, err := net.Listen("tcp", *grpcAddr)
		if err != nil {
			logger.Log("transport", "gRPC", "during", "Listen", "err", err)
			os.Exit(1)
		}
		g.Add(func() error {
			logger.Log("transport", "gRPC", "addr", *grpcAddr)
			baseServer := grpc.NewServer()
			pb.RegisterGreeterServer(baseServer, grpcServer)
			return baseServer.Serve(grpcListener)
		}, func(error) {
			grpcListener.Close()
		})
	}
	{
		// This function just sits and waits for ctrl-C.
		cancelInterrupt := make(chan struct{})
		g.Add(func() error {
			c := make(chan os.Signal, 1)
			signal.Notify(c, syscall.SIGINT, syscall.SIGTERM)
			select {
			case sig := <-c:
				return fmt.Errorf("received signal %s", sig)
			case <-cancelInterrupt:
				return nil
			}
		}, func(error) {
			close(cancelInterrupt)
		})
	}
	logger.Log("exit", g.Run())
}

func usageFor(fs *flag.FlagSet, short string) func() {
	return func() {
		fmt.Fprintf(os.Stderr, "USAGE\n")
		fmt.Fprintf(os.Stderr, "  %s\n", short)
		fmt.Fprintf(os.Stderr, "\n")
		fmt.Fprintf(os.Stderr, "FLAGS\n")
		w := tabwriter.NewWriter(os.Stderr, 0, 2, 2, ' ', 0)
		fs.VisitAll(func(f *flag.Flag) {
			fmt.Fprintf(w, "\t-%s %s\t%s\n", f.Name, f.DefValue, f.Usage)
		})
		w.Flush()
		fmt.Fprintf(os.Stderr, "\n")
	}
}
```
现在，你或许注意到了，我挂载一组日志中间件。它允许我从主服务/后端服务流程中脱离日志逻辑。
```go
package greeterservice

import (
	"time"

	"github.com/go-kit/kit/log"
)

// ServiceMiddleware describes a service middleware.
type ServiceMiddleware func(Service) Service

// LoggingMiddleware takes a logger as a dependency and returns a ServiceMiddleware.
func LoggingMiddleware(logger log.Logger) ServiceMiddleware {
	return func(next Service) Service {
		return loggingMiddleware{next, logger}
	}
}

type loggingMiddleware struct {
	Service
	logger log.Logger
}

func (m loggingMiddleware) Health() (healthy bool) {
	defer func(begin time.Time) {
		m.logger.Log(
			"method", "Health",
			"healthy", healthy,
			"took", time.Since(begin),
		)
	}(time.Now())
	healthy = m.Service.Health()
	return
}

func (m loggingMiddleware) Greeting(name string) (greeting string) {
	defer func(begin time.Time) {
		m.logger.Log(
			"method", "Greeting",
			"name", name,
			"greeting", greeting,
			"took", time.Since(begin),
		)
	}(time.Now())
	greeting = m.Service.Greeting(name)
	return
}
```
``` go
package greeterendpoint

import (
	"context"
	"time"

	"github.com/go-kit/kit/endpoint"
	"github.com/go-kit/kit/log"
)

// LoggingMiddleware returns an endpoint middleware that logs the
// duration of each invocation, and the resulting error, if any.
func LoggingMiddleware(logger log.Logger) endpoint.Middleware {
	return func(next endpoint.Endpoint) endpoint.Endpoint {
		return func(ctx context.Context, request interface{}) (response interface{}, err error) {
			defer func(begin time.Time) {
				logger.Log("transport_error", err, "took", time.Since(begin))
			}(time.Now())
			return next(ctx, request)
		}
	}
}
```

### Gizmo greeter
我同样使用Go Kit框架的开发方式，使用了Gizmo服务。我定义了四个包，服务、后端、传输层和服务发现注册。

服务的实现和服务发现注册的大袋使用的和Go Kit的是一样的。但是后端和传输层的定义采用更符合Go Kit特性的方式来写。
``` go
ackage greeterendpoint

import (
	"net/http"

	ocontext "golang.org/x/net/context"

	"github.com/NYTimes/gizmo/server"
	"github.com/antklim/go-microservices/gizmo-greeter/pkg/greeterservice"
)

// Endpoints collects all of the endpoints that compose a greeter service.
type Endpoints struct {
	HealthEndpoint   server.JSONContextEndpoint
	GreetingEndpoint server.JSONContextEndpoint
}

// MakeServerEndpoints returns service Endoints
func MakeServerEndpoints(s greeterservice.Service) Endpoints {
	healthEndpoint := MakeHealthEndpoint(s)
	greetingEndpoint := MakeGreetingEndpoint(s)

	return Endpoints{
		HealthEndpoint:   healthEndpoint,
		GreetingEndpoint: greetingEndpoint,
	}
}

// MakeHealthEndpoint constructs a Health endpoint.
func MakeHealthEndpoint(s greeterservice.Service) server.JSONContextEndpoint {
	return func(ctx ocontext.Context, r *http.Request) (int, interface{}, error) {
		healthy := s.Health()
		return http.StatusOK, HealthResponse{Healthy: healthy}, nil
	}
}

// MakeGreetingEndpoint constructs a Greeting endpoint.
func MakeGreetingEndpoint(s greeterservice.Service) server.JSONContextEndpoint {
	return func(ctx ocontext.Context, r *http.Request) (int, interface{}, error) {
		vars := r.URL.Query()
		names, exists := vars["name"]
		if !exists || len(names) != 1 {
			return http.StatusBadRequest, errorResponse{Error: "query parameter 'name' required"}, nil
		}
		greeting := s.Greeting(names[0])
		return http.StatusOK, GreetingResponse{Greeting: greeting}, nil
	}
}

// HealthRequest collects the request parameters for the Health method.
type HealthRequest struct{}

// HealthResponse collects the response values for the Health method.
type HealthResponse struct {
	Healthy bool `json:"healthy,omitempty"`
}

// GreetingRequest collects the request parameters for the Greeting method.
type GreetingRequest struct {
	Name string `json:"name,omitempty"`
}

// GreetingResponse collects the response values for the Greeting method.
type GreetingResponse struct {
	Greeting string `json:"greeting,omitempty"`
}

type errorResponse struct {
	Error string `json:"error"`
}
```
如你所见，同Go Kit的代码片段很相似。只要区别在于应该返回的接口类型：
``` go
package greetertransport

import (
	"context"

	"github.com/NYTimes/gizmo/server"
	"google.golang.org/grpc"

	"errors"
	"net/http"

	"github.com/NYTimes/gziphandler"
	pb "github.com/antklim/go-microservices/gizmo-greeter/pb"
	"github.com/antklim/go-microservices/gizmo-greeter/pkg/greeterendpoint"
	"github.com/sirupsen/logrus"
)

type (
	// TService will implement server.RPCService and handle all requests to the server.
	TService struct {
		Endpoints greeterendpoint.Endpoints
	}

	// Config is a struct to contain all the needed
	// configuration for our JSONService.
	Config struct {
		Server *server.Config
	}
)

// NewTService will instantiate a RPCService with the given configuration.
func NewTService(cfg *Config, endpoints greeterendpoint.Endpoints) *TService {
	return &TService{Endpoints: endpoints}
}

// Prefix returns the string prefix used for all endpoints within this service.
func (s *TService) Prefix() string {
	return ""
}

// Service provides the TService with a description of the service to serve and
// the implementation.
func (s *TService) Service() (*grpc.ServiceDesc, interface{}) {
	return &pb.Greeter_serviceDesc, s
}

// Middleware provides an http.Handler hook wrapped around all requests.
// In this implementation, we're using a GzipHandler middleware to
// compress our responses.
func (s *TService) Middleware(h http.Handler) http.Handler {
	return gziphandler.GzipHandler(h)
}

// ContextMiddleware provides a server.ContextHAndler hook wrapped around all
// requests. This could be handy if you need to decorate the request context.
func (s *TService) ContextMiddleware(h server.ContextHandler) server.ContextHandler {
	return h
}

// JSONMiddleware provides a JSONEndpoint hook wrapped around all requests.
// In this implementation, we're using it to provide application logging and to check errors
// and provide generic responses.
func (s *TService) JSONMiddleware(j server.JSONContextEndpoint) server.JSONContextEndpoint {
	return func(ctx context.Context, r *http.Request) (int, interface{}, error) {

		status, res, err := j(ctx, r)
		if err != nil {
			server.LogWithFields(r).WithFields(logrus.Fields{
				"error": err,
			}).Error("problems with serving request")
			return http.StatusServiceUnavailable, nil, errors.New("sorry, this service is unavailable")
		}

		server.LogWithFields(r).Info("success!")
		return status, res, nil
	}
}

// ContextEndpoints may be needed if your server has any non-RPC-able
// endpoints. In this case, we have none but still need this method to
// satisfy the server.RPCService interface.
func (s *TService) ContextEndpoints() map[string]map[string]server.ContextHandlerFunc {
	return map[string]map[string]server.ContextHandlerFunc{}
}

// JSONEndpoints is a listing of all endpoints available in the TService.
func (s *TService) JSONEndpoints() map[string]map[string]server.JSONContextEndpoint {
	return map[string]map[string]server.JSONContextEndpoint{
		"/health": map[string]server.JSONContextEndpoint{
			"GET": s.Endpoints.HealthEndpoint,
		},
		"/greeting": map[string]server.JSONContextEndpoint{
			"GET": s.Endpoints.GreetingEndpoint,
		},
	}
}
```
``` go
package greetertransport

import (
	pb "github.com/antklim/go-microservices/gizmo-greeter/pb"
	ocontext "golang.org/x/net/context"
)

// Greeting implementation of the gRPC service.
func (s *TService) Greeting(ctx ocontext.Context, r *pb.GreetingRequest) (*pb.GreetingResponse, error) {
	return &pb.GreetingResponse{Greeting: "Hola Gizmo RPC " + r.Name}, nil
}
```
值得关注的不同点在与Go Kit 和 Gizmo对于传输层的实现上。Gizmo提供了供你使用的服务类型。不论我将HTTP路径映射到后端定义上，或者是更低级的HTTP 请求/响应，都可以通过Gizmo来控制。


## 结论
Go Micro框架是微服务框架中部署速度最快的。框架中还有很多的特性。所以你不需要重复造轮子。但是，拥有着舒适和速度的同时，缺失了灵活性。它不像是Go Kit那样可以简单升级系统中部分内容。而且，它是强制使用gRPC作为通信方式的。

对于Go Kit，可能需要一段时间来适应并学习。它体现了Go语言良好的特性和经验丰富的软件架构。在其他方面，它没有其他框架的那种条条框框。所有部分都可以单独的修改或者是升级。

Gizmo套件是居于Go Micro和Go Kit中间的一款微服务框架。它提供了更高层次抽象的服务包。但是它的文档和代码示例确实还是有些薄弱。我不得不通过源码来理解不同的服务类型是如何工作的。Gizmo运行起来要比Go Kit简单，但是没有Go Micro平滑。


[原文地址](https://medium.com/seek-blog/microservices-in-go-2fc1570f6800)

