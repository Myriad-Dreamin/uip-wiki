



# Attestation

attestation是一种特殊的action。也即具有以下形式`[pre data, signature, signature_type]`。第一个attestation中携带原信息，可以是加密过的，也可以是原数据。此后多方对前一次签名的签名`signature`再次签名，形成多阶签名。

在ISC开始的时候，每个transaction都会有基于transaction intent的一阶签名。

attestation信任产生的根源是:

+ NSB是可靠的信任来源。
+ 原信息被ISC相关的用户在NSB上确认，构成了此签名链最初的信任。
+ 数字签名在至少多达$2^{128}$大小的值域上足够随机。