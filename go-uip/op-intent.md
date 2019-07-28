# Op-Intent

OpIntent目前分为两种：PaymentIntent与ContractInvocationIntent。

当OpIntent以json格式呈现时，总是有以下字段：

```json
{
	"name": "<string>", // 必选
	"op_type": "<string>", // 必选
    "meta": "<any-value>" // 可选
	...
}
```

Name可以是任何字符串，要求在一组OpIntents中唯一。

OpType如果是`"Payment"`，那么代表这是PaymentIntent，如果是`"ContractInvocation"`，那么代表这是ContractInvocationIntent。

对于PaymentIntent，可以有以下字段：

```json
{
	"src": "<uip.Account>", // 必选
	"dst": "<uip.Account>", // 必选
	"amount": "<hexstring>", // 必选
	"unit": "<string>" // 可选
}
```

对于ContractInvocationIntent，总是有以下字段：

```json
{
	"invoker": "<uip.Account>", // 必选
	"contract_addr": "<bytes>", // 必选
	"contract_code": "<bytes>", // 必选
	"func": "<string>", // 必选
	"parameters": ["<uip.Params>", "<uip.Params>", "..."] // 必选
}
```

一个Params字段，具有以下两种形式之一：

```json
"Params": {
    "Type": "<string>",
    "Value": "<uip.ConstParamValue>|<uip.ParamValue>"
}
"ConstParamValue": {
    "constant": "<any-value>"
}
"ParamValue": {
    "contract": "<string>",
    "pos": "<hexstring>",
    "field": "<string>|<bytes>"
}

```

例如在Solidity中，一个Params被描述成：

```json
{
    "Type": "uint256",
    "Value": {
        "constant": "0x00"
    }
}
```

或是被描述成：

```json
{
    "Type": "uint256",
    "Value": {
        "contract": "some_contract",
        "pos": "0x00",
        "field": "genuine value"
    }
}
```

经过一段时间后，这个值可能变化，所以具有不确定性。但一定可以被Data MerkleProof唯一确认。