# ves

ves的全称为verifiable execution system，旨在将多链行为分解为多个单链行为。

ves拥有下列职责：

+ 要求一切行为都能经受检验，这个能力ves通过nsb间接实现。
+ 要求具有op intent的实例化能力，这个能力ves通过go-uip这个协议库与其他ves以及服务的用户达成共识。
+ 要求能够维护一组op intent的完整运行或者错误的责任落实。

ves正是在完成这些职责的过程中盈利。

当一个ves-client（记为$\mathsf{v}_0$）发起一组交易时，ves-client会以op intent的形式发送给ves。ves随即响应，向与op intent相关的剩余的ves-client($\mathsf{v}_1,\mathsf{v}_2,\dots,\mathsf{v}_n$)发起请求。isc(nsb上以session为单位的合约)要求所有的ves-client都同意ves上传到isc上半实例化的op intent，也就是transaction intent。注意transaction intent并不是能够上链的transaction。ves在输入的op intent会在有限步内执行完全的前提下，会考虑一种能够使得每一个transaction intent都能在它被执行的时候被完全实例化的顺序，也就是说，具有多链能力的op intent会事先转化为多个单链行为的transaction intent，而只有在执行时transaction intent才会转化能够上链（被链识别）的on-chain transaction。更为形式化的定义参见uip协议。

为了完成这些职责，ves提供以下的rpc方法。

#### `SessionStart`

+ `->`: `opintents OpIntents`
+ `<-`: `ok bool`
+ `<-`: `session_id bytes`

#### `SessionAckForInit`

+ `->`: `session_id bytes`
+ `->`: `user Account`
+ `->`: `user_signature Signature`
+ `<-`: `ok bool`

#### `SessionRequireRawTransaction`

+ `->`: `session_id bytes`
+ `<-`: `raw_transaction bytes`
+ `<-`: `tid uint64`
+ `<-`: `src Account`
+ `<-`: `dst Account`

#### `InformAttestation`

+ `->`: `session_id bytes`
+ `->`: `atte Attestation`
+ `<-`: `ok bool`

