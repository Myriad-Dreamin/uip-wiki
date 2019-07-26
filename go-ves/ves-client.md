# ves-client

ves-client为一组命令行接口。

```bash
set-name <name>
```

重新设置客户端名称

```bash
register-key <file-path>
```

注册私钥到文件映射

```bash
register-eth <file-path>
```

注册ethereum账号到文件映射

```bash
send-eth-alias-to-ves <alias>
```

注册ethereum公钥关系到ves远程服务

```bash
send-alias-to-ves <alias>
```

注册私钥映射中某个对应的公钥到ves远程服务

```bash
keys
```

查看当前文件映射中所有的公钥

```bash
send-op-intents <file-path>
```

发送一组op-intent
