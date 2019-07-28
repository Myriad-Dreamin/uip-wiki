# Pay between chains

此次试验的ves版本为0.6.0，地址：https://github.com/Myriad-Dreamin/go-ves/tree/v0.6.0

uip地址版本为：https://github.com/Myriad-Dreamin/go-uip/tree/21f0f54

假设服务器已经识别$a_1(\text{0x93334ae4b2d42ebba8cc7c797bfeb02bfb3349d6})$,$a_2(\text{0x47a1cdb6594d6efed3a6b917f2fbaa2bbcf61a2e})$为某一用户，创建以下文件，命名为`intent.json`

```json
{
    "name":    "Op1",
    "op_type": "Payment",
    "src": {
        "domain":    2,
        "user_name": "a1",
    },
    "dst": {
        "domain":    1,
        "user_name": "a2",
    },
    "amount": "02e0",
    "unit":   "wei",
}
```

使用`ves-client`执行操作。

#### vesc1

使用`register-eth a1.json`将此json内的信息注册到文件映射。

```json
[
    {
        "address": "d051a43d3ea62afff3632bca3d5abf68bc6fd737",
        "chain_id": 1,
        "alias": "eth1"
    },
    {
        "address": "93334ae4b2d42ebba8cc7c797bfeb02bfb3349d6",
        "chain_id": 2,
        "alias": "eth2"
    }
]
```

使用`send-eth-alias-to-ves eth2`发送第二个地址给服务器，因为服务器记录中$a_1$为此地址。

同样地，对**vesc2**做此步骤。

```json
[
    {
        "address": "47a1cdb6594d6efed3a6b917f2fbaa2bbcf61a2e",
        "chain_id": 1,
        "alias": "eth1"
    },
    {
        "address": "981739a13593980763de3353340617ef16da6354",
        "chain_id": 2,
        "alias": "eth2"
    }
]
```

发送：

执行以下命令：

`send-op-intents intent.json`

程序自动执行。![pre](pre.gif)

左下角为central-ves，右下角为grpc-ves。

有关代码仍在进一步改进中。