# Start

示例见https://github.com/HyperService-Consortium/go-ves/blob/master/test/invoke-test/main_test.go

注解如下：

```go
func TestTransfer(t *testing.T) {
	
    go func() {
		// profile 服务器
        err := http.ListenAndServe("0.0.0.0:22239", nil)
		if err != nil {
			panic(err)
		}
	}()
	
    // 注册vesclient的handler
	go vesclient.StartDaemon()
	h := sugar.NewHandlerErrorLogger(t)
	
    // 以下均为logger注册
	f, err := os.Create("gin-server.log")
	if err != nil {
		t.Fatal(err)
	}
	gin.DefaultWriter = f
	defer f.Close()


	cfg0, cfg1, cfg2 := zap.NewDevelopmentConfig(), zap.NewDevelopmentConfig(), zap.NewDevelopmentConfig()
	cfg3, cfg4 := zap.NewDevelopmentConfig(), zap.NewDevelopmentConfig()

	//cfg0.OutputPaths = []string{"ves-client.log"}
	//cfg1.OutputPaths = []string{"aws-client.log"}
	//cfg2.OutputPaths = []string{"qwq-client.log"}
	//cfg3.OutputPaths = []string{"central-server.log"}
	//cfg4.OutputPaths = []string{"ves-server.log"}

	cfg0.OutputPaths = []string{"stdout"}
	cfg1.OutputPaths = []string{"stdout"}
	cfg2.OutputPaths = []string{"stdout"}
	cfg3.OutputPaths = []string{"stdout"}
	cfg4.OutputPaths = []string{"stdout"}

	//vesLogger, awsLogger, qwqLogger, cVesServerLogger, vesServerLogger :=
	//	h.HandlerError(logger.NewZapLogger(cfg0, zapcore.DebugLevel)).(logger.Logger),
	//	h.HandlerError(logger.NewZapLogger(cfg1, zapcore.DebugLevel)).(logger.Logger),
	//	h.HandlerError(logger.NewZapLogger(cfg2, zapcore.DebugLevel)).(logger.Logger),
	//	h.HandlerError(logger.NewZapLogger(cfg1, zapcore.DebugLevel)).(logger.Logger),
	//	h.HandlerError(logger.NewZapLogger(cfg2, zapcore.DebugLevel)).(logger.Logger)

	vesLogger, awsLogger, qwqLogger, cVesServerLogger, vesServerLogger :=
		h.HandlerError(logger.NewZapLogger(cfg0, zapcore.DebugLevel)).(logger.Logger).With("from", "ves"),
		h.HandlerError(logger.NewZapLogger(cfg1, zapcore.DebugLevel)).(logger.Logger).With("from", "user1"),
		h.HandlerError(logger.NewZapLogger(cfg2, zapcore.DebugLevel)).(logger.Logger).With("from", "user2"),
		h.HandlerError(logger.NewZapLogger(cfg1, zapcore.DebugLevel)).(logger.Logger).With("from", "cves"),
		h.HandlerError(logger.NewZapLogger(cfg2, zapcore.DebugLevel)).(logger.Logger).With("from", "ves")
	
    // 重置configuration
	config.ResetPath(cfgPath)
	var cfg = config.Config()
	db := dbinstance.MakeDB()
	go func() {
		// 启动cves服务
        if err := h.HandlerError(
			centered_ves_server.NewServer(
				cVesPort, cVesAddr, db, centered_ves_server.NSBHostOption(nsbHost), cVesServerLogger,
			)).(*centered_ves_server.Server).Start(); err != nil {
			t.Fatalf("ListenAndServe: %v\n", err)
		}
	}()
	signer := h.HandlerError(signaturer.NewTendermintNSBSigner(
		h.HandlerError(hex.DecodeString("2333bfffffffffffffff2333bbffffffffffffff2333bbffffffffffffffffff2333bfffffffffffffff2333bbffffffffffffff2333bbffffffffffffffffff"),
	).([]byte))).(*signaturer.TendermintNSBSigner)

	muldb, sindb := Prepare()
    // ves服务器
	var server = h.HandlerError(ves_server.NewServer(
		muldb, sindb, multi_index.XORMMigrate, signer,
		ves_server.NSBHostOption(nsbHost), vesServerLogger)).(*ves_server.Server)

	go func() {
		if err := server.ListenAndServe(cfg.ServerConfig.Port, cfg.ServerConfig.CentralVesAddress); err != nil {
			t.Fatal(err)
		}
	}()

	time.Sleep(time.Millisecond * 100)
	
    // 使用默认的client与服务器交互
	ves, aws, qwq :=
		h.HandlerError(vesclient.VanilleMakeClient("ves", testServer, vesLogger)).(*vesclient.VesClient),
		h.HandlerError(vesclient.VanilleMakeClient("awsl", testServer, awsLogger)).(*vesclient.VesClient),
		h.HandlerError(vesclient.VanilleMakeClient("qwq", testServer, qwqLogger)).(*vesclient.VesClient)
	
    // 将配置导入（只用导入一次，这里每次都导入只是图方便）
	var b = make([]byte, 65536)
	h.HandlerError0(ves.ConfigEth("./json/veth.json", b))
	h.HandlerError0(aws.ConfigEth("./json/leth.json", b))
	h.HandlerError0(qwq.ConfigEth("./json/qeth.json", b))
	h.HandlerError0(ves.ConfigKey("./json/vesa.json", b))
	h.HandlerError0(aws.ConfigKey("./json/lswa.json", b))
	h.HandlerError0(qwq.ConfigKey("./json/qwq.json", b))
	var wg = false
	var mx sync.Mutex
	var ch = make(chan bool)
	ves.SubscribeCloseSession(func(sess []byte) {
		fmt.Println("session closed", "session", sess, "ves")
		mx.Lock()
		defer mx.Unlock()
		if wg == false {
			ch <- true
			wg = true
		}
	})
	aws.SubscribeCloseSession(func(sess []byte) {
		fmt.Println("session closed", "session", sess, "aws")
		mx.Lock()
		defer mx.Unlock()
		if wg == false {
			ch <- true
			wg = true
		}
	})
	qwq.SubscribeCloseSession(func(sess []byte) {
		fmt.Println("session closed", "session", sess, "qwq")
		mx.Lock()
		defer mx.Unlock()
		if wg == false {
			ch <- true
			wg = true
		}
	})

	// 开启一次op intent
	h.HandlerError0(ves.SendOpIntents("./json/intent.json", b))
	<-ch
}

```

这只是一个用于测试的自动化脚本，实际的ves-client可以自己动手写满足不同的要求。

并且需要注意，参与的账户需要注册在服务器上，这里提前注册过了，所以不需要再注册了。

注册eth账户的方法参考https://github.com/HyperService-Consortium/go-ves/blob/master/lib/net/ves-client/write.go#L109

注册tendermint账户的方法参考https://github.com/HyperService-Consortium/go-ves/blob/master/lib/net/ves-client/write.go#L121

所谓的alias即在json配置中的https://github.com/HyperService-Consortium/go-ves/blob/master/test/invoke-test/json/qeth.json#L5

并且注意，同一个chain_id上的`(chain_id, address)`组成的元组必须唯一！否则cves会不知道将某个需要发往`(chain_id, address)`对应client的请求发往何处。

## Chain DNS

chain dns是一个设想，但还没有实现。这里有一个mock的方法。修改

https://github.com/HyperService-Consortium/go-ves/blob/master/config/host-map.go#L10

https://github.com/HyperService-Consortium/go-ves/blob/master/config/chain-info.go

这两个文件。

### Op Intents

参考https://github.com/HyperService-Consortium/go-ves/blob/master/test/invoke-test/json/intent.json

现在关于

```json
"domain":    2,                       "contract":"263fef3fe76fd4075ac16271d5115d01206d3674",
"pos": "01",
"field": "strikePrice"
```

这样的支持还不算好，将来再做改进