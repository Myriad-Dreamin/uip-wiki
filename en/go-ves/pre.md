# go-ves

go-ves是以golang实现的ves，对应uip中的ves概念。现在在实验阶段也许是中心化的，但它可以被去中心化。

go-ves包括了三个主要的部分。

第一个部分为ves-client，目前只有golang的cmd实现。ves-client是用户与ves交互的界面接口。ves-client中包含一些对本地私钥的管理，并自动地响应ves的请求。未来你可以对ves-client做一些细化的配置以使其满足实际的需求。

第二个部分为ves，ves提供了一组grpc的api接口，所有api以完成一组op-intent为目展开实现。

第三个部分为central-ves，负责维护一组用户的状态与ves之间的任务派发与负载均衡。central-ves实际上成为了组织ves的枢纽与ves服务提供的中心端口。

