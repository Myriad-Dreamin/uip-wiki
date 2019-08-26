# central-ves

为了方便制造p2p中心，引出一个额外的概念

central-ves负责维护一组用户的状态与ves之间的任务派发与负载均衡。

在现代网络下小规模应用用户很难独立访问到其他用户，因此central-ves负责起到桥梁和解析作用。

central-ves拥有下列职责：

- 维持用户连接状态，或用户请求缓冲
- 提供ves集群信赖的nsb区块链网络地址
- 提供当前可用的某个ves grpc服务
- 提供转发用户间请求的能力

目前，central-ves与ves-client之间采用websocket+protobuf的方式传递信息。

#### `ClientHello`

- `->`: `name bytes`
- `<-`: `grpc_host bytes`
- `<-`: `nsb_host bytes`

ClientHello负责将自身name信息注册到central-ves上。如果需要增加额外的方法，如`登录检测`等，可以选择继承`central-ves`后修改此方法，或者注册中间件。

#### `RequestComing`

- `->`: `session_id bytes`
- `->`: `account Account`
- `->`: `grpc_host bytes`
- `->`: `nsb_host bytes`
- `<-`: `ok bool`

`RequestComing`接受来自ves的信息，转发至客户端。

#### `RequestGrpcService`
- `->`: `name bytes`
- `<-`: `grpc_host bytes`

`RequestGrpcService`接受客户端的信息，central-ves返回任意一个可用的grpc服务的地址给带有name名称的客户端。

#### `RequestNsbService`
- `->`: `name bytes`
- `<-`: `nsb_host bytes`

`RequestNsbService`接受客户端的信息，central-ves返回任意一个可用的nsb服务的地址给带有name名称的客户端。

#### `SessionList`
- `->`: `name bytes`
- `<-`: `session_ids repeated bytes`
- `<-`: `grpc_hosts repeated bytes`
- `<-`: `default_nsb_host bytes`

`SessionList`接受客户端的信息，central-ves返回一组待响应的session信息。每个记录的session，包括此session的guid，负责此session的grpc服务地址，可选的nsb服务地址。

#### `TransactionList`
- `->`: `name bytes`
- `<-`: `transactions repeated bytes`
- `<-`: `grpc_hosts repeated bytes`
- `<-`: `default_nsb_host bytes`

`TransactionList`接受客户端的信息，central-ves返回一组待响应的transaction信息。每个记录的transaction都是可以计算过以后可以上链的，并且额外会提供负责此transaction的grpc服务地址，可选的nsb服务地址。

#### `UserRegister`
- `->`: `account Account`
- `->`: `user_name string`
- `<-`: `ok bool`

ClientHello负责将自身account信息注册到central-ves上。如果需要增加额外的方法，如`同一链上重复账户`等，可以选择继承`central-ves`后修改此方法，或者注册中间件。

#### `AttestationReceive`

- `->`: `session_id bytes`
- `->`: `src Account`
- `->`: `dst Account`
- `->`: `atte Account`
- `->`: `grpc_host bytes`
- `<-`: `ok bool`

`AttestationReceive`在客户端之间流动，由central-ves进行转发。这个信息仅仅是可选的通知，并非权威性的。未来可能会去除。

#### `CloseSession`

- `->`: `session_id bytes`
- `->`: `grpc_host bytes`
- `->`: `nsb_host bytes`
- `<-`: `ok bool`

`CloseSession`仅仅用于通知客户端，此session已停止。
