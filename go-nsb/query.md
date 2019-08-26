# Query Status on NSB

目前NSB可供查询的内容不多，主要包括两个方面，账户查询和merkle proof。

+ acc_getAccInfo: 查询account信息。
+ prove on xxx trie: 查询在xxx trie上的数据。
+ prove get storage at: 查询在account_trie上对应合约账户的stroage trie某数据的存在性。

其中xxx可以为state, transaction, account, action, valid merkle proof, valid on chain merkle proof 六棵mpt中的任意一棵。