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

#### `RequestComing`

- `->`: `session_id bytes`
- `->`: `account Account`
- `->`: `grpc_host bytes`
- `->`: `nsb_host bytes`
- `<-`: `ok bool`

#### `RequestGrpcService`
- `->`: `name bytes`
- `<-`: `grpc_host bytes`

#### `RequestNsbService`
- `->`: `name bytes`
- `<-`: `nsb_host bytes`

#### `SessionList`
- `->`: `name bytes`
- `<-`: `session_ids repeated bytes`
- `<-`: `grpc_hosts repeated bytes`
- `<-`: `default_nsb_host bytes`

#### `TransactionList`
- `->`: `name bytes`
- `<-`: `transactions repeated bytes`
- `<-`: `grpc_hosts repeated bytes`
- `<-`: `default_nsb_host bytes`

#### `UserRegister`
- `->`: `account Account`
- `->`: `user_name string`
- `<-`: `ok bool`

#### `SessionRequestForInit`
- `->`: `session_id bytes`
- `<-`: `opintents OpIntents`

#### `AttestationReceive`
- `->`: `session_id bytes`
- `->`: `src Account`
- `->`: `dst Account`
- `->`: `atte Account`
- `->`: `grpc_host bytes`
- `<-`: `ok bool`

#### `CloseSession`
- `->`: `session_id bytes`
- `->`: `grpc_host bytes`
- `->`: `nsb_host bytes`
- `<-`: `ok bool`

