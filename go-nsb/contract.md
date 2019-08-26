# Contract

不同于go-ethereum等的方便性。NSB的contract的代码需要在共识确认前被确认。

链通过暴露contract私有的地址和storage让contract代码完成transaction分配给它的任务。

每次原子性的操作 (transaction)，contract都接收到一组信息：

+ `Source account`: 操作contract的账户
+ `Destination contract address`: 目标contract的地址
+ `Value uint256`: 操作contract的账户在transaction中明确的在这次操作中所能忍受的`Value`上限。
+ `FunctionName string`: 此次操作的名称。
+ `Args []byte`: 此次操作参数列表序列化后的内容。
+ `LocalStorage struct`: 每一个目标contract的地址持久化数据的存储。

相当于以下操作：

```c++
auto contract = (*MyContract)(Destination)
contract->Storage = LocalStorage
return contract->caller(Source)
               ->call_with_tolerance(Value)
               ->FunctionName(unpack(Args))
```



