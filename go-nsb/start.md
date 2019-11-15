# 开始

环境需要Linux系统，如果是Windows系统，请安装docker以后运行。

### 直接运行

运行abci：

```bash
go run nsb_cli.go
```

初始化Tendermint，并启动：

```bash
tendermint init --home ./nsb
tendermint --rpc.laddr tcp://0.0.0.0:26657 --home ./nsb --proxy_app tcp://0.0.0.0:27667
```

打开`localhost:26657`验证NSB是否启动。

### Docker单节点及集群

运行单节点：

在docker文件夹下运行：

```bash
python makefile.py build0
```

重新启动：

```bash
python makefile.py start0
```

终止服务：

```bash
python makefile.py stop0
```

删除服务：

```bash
python makefile.py down0
```

删除所有数据

```bash
python makefile.py clean
```

运行四节点：

在docker文件夹下运行：

```bash
python makefile.py build
```

重新启动：

```bash
python makefile.py start
```

终止服务：

```bash
python makefile.py stop
```

删除服务：

```bash
python makefile.py down
```

删除所有数据

```bash
python makefile.py clean
```

