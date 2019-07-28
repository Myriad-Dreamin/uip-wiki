# Signer



在所有的区块链中，普遍使用的是签名技术，所以我们选择把Signer作为bni的第一个选项类型。

我们的抽象接口参照的是ECC方式。

```go
type signatureType = uint32
type signatureContent = []byte
type signedContent = []byte
type Signature interface {
	GetSignatureType() signatureType
	GetContent() signedContent
}

type publicKey []byte
type Signer interface {
	GetPublicKey() publicKey
	Sign(signatureContent) Signature
}
```

`GetPublicKey`用于指定`Signer`的公开身份，`Sign`用于对`[]byte`做一次特征抽取，生成一个带有内容的`Signature`。

`Signature`中包含两个接口，一个用于鉴别`Signature`类型，让网络中的其他entity可以根据类型验证你的`Signature`；另一个用于得到签名的特征。如果这个`Signer`完成的是ECC，那么`S[signatureType].recover(signatureContent, signedContent, Signer.PublicKey) == true`

