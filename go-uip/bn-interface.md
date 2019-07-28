# bn interface

每个类型的链只需要编写一次bn interface供ves和atomizer使用。

```go
type chainID = uint64
type provedData = map[string][]byte
type rawTransaction = []byte
type information = []byte
type address = []byte
```

#### Translator

Translator是blockchain network interface(bni)的第一个部分

```go
type Translator interface {
	Translate(*TransactionIntent, provedData) (rawTransaction, error)
}
```

Translate的功能是，将TransactionIntent结合provedData生成一个可以被执行的on-chain Transaction。

TransactionIntent中包含一个自定义的meta段，它包含某一种类型blockchain自定义的meta段。当然，contractInvokeIntent的invoke information也在meta段里。

例如：
当某一条链具有自定义的`ip`段，它要求intent的transaction必须经过某一个中继服务器处理以后再上链。那么在原始的op-intent的meta段中添加：
```json
meta: {
	"ip": "http://127.0.0.1:8546"
}
```

则在对应的TransactionIntent的meta段中都包含这一个选项，供Translate处理。


#### Router

Router是bni的第二个部分

```go
type Router interface {
	RouteRaw(chainID, rawTransaction) (information, error)
	
	MustWithSigner() bool
	RouteWithSigner(Signer) Router
}
```

RouteRaw的功能是，将具有chainID编号的原始Transaction发送到对应链上。

为什么考虑直接传入chainID而不是chainID对应的host。因为这样灵活性更好，自定义一个RouteRaw可以像ethereum那样使用rpc，也可以传输到某一个专职的服务器上，让它代你完成这个RouteRaw的任务。

传入的rawTransaction必定是经过Translate处理的，所以也有可能会包含选项：
```json
rawTransaction: {
	"my_raw_transaction": "content",
	"must_send_to": "ip: http://127.0.0.1:8546"
}
```

#### Checker

Checker是bni的第三个部分：

```go
type Checker interface {
	CheckAddress(address) bool
	// CheckMeta(meta) bool
}
```

在这一部分，Checker编写一些区块的合法性检查。未来可能还有一些其他的合法性检查被加入Checker Interface。


#### BlockChainInterface

所有的这些组成了BNI，你可以分部分实现它，完成多种组合，也可以直接创建一个结构体完成三种类型接口的实现。

```go
type BlockChainInterface interface {
	Router
	Translator
	Checker
}
```

#### 编写属于自己的BlockChainInterface
下面是

```go
import types "github.com/Myriad-Dreamin/go-uip/types"
type BlockChainInterfaceTest struct {
	types.BaseBlockChainInterface
	Signer types.Signer
}

func (bn *BlockChainInterfaceTest) MustWithSigner() bool {
	return true
}

func (bn *BlockChainInterfaceTest) RouteWithSigner(signer Signer) Router {
	var nbn = *bn
	nbn.Signer = signer
	return &nbn
}

func (bn *BlockChainInterfaceTest) RouteRaw(uint64, []byte) ([]byte, error) {
	// do something
	return nil, errors.New("must impl method RouteRaw(cid, rtx) (info, err)")
}

func (bn *BlockChainInterfaceTest) Translate(*TransactionIntent, map[string][]byte) ([]byte, error) {
	// do something
	return nil, errors.New("must impl method Translate(tx, kvs) (rtx, err)")
}
```

并在BlockChainInterfaceFactory中注册你的BlockChainInterface。