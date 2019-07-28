# Chain ID & Account

## Chain ID

每一条链都被标记为唯一一个链的编号，链编号长度为8字节。例如，当链编号为`0.0.0.3`时，这一串数字唯一指定了整个网络中的NSB链。

## Account

uip规定一个有意义的Account是以下二元组：

+ `uint64(8 octets)`: `chain-id`
+ `octets`: `address`

可以按照以下格式压缩：

| 8 octets | 2 octets | octets  |
| :------: | :------: | :-----: |
| chain-id |   len    | address |

一般每一条链上有且仅有一种地址的格式，因此address总不是变长的。所以也可以按照以下格式压缩：

| 8 octets | octets  |
| :------: | :-----: |
| chain-id | address |

当chain-id增加时，这种压缩方式会有问题。

目前Account约定的json格式为：

```json
{
	"user_name": "<string>",
	"domain": "<uint64>",
}
```

