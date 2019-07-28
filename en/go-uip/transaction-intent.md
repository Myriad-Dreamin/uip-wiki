# transaction-intent

无论op-intent怎么变动，它总是会变成下面的golang结构体。

```go
type hexstring string
type TransactionIntent struct {
	TransType uint64    `json:"trans_type"`
	Src       []byte    `json:"src"`
	Dst       []byte    `json:"dst"`
	Meta      []byte    `json:"meta"`
	Amt       hexstring `json:"amt"`
	ChainId   uint64    `json:"chain_id"`
}
```

这同样也指定了一种对应的json格式。

TransType拥有下列枚举值：

| Value | Type           |
| ----- | -------------- |
| 0     | Payment        |
| 1     | ContractInvoke |

