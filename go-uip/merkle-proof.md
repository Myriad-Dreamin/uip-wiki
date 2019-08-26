# merkle proof

merkle proof亦非什么新颖的概念。

在go uip中，merkle proof被描述为具有下列属性的对象：

+ Type uint16: 描述merkle proof的类型。
+ RootHash []byte: 表示merkle proof的根hash。
+ Proof []byte: 被序列化的证明内容。
+ Key/Value []byte: 被merkle proof证明存在性的键值对。

任意merkle tree显然都具有root，并且这个root结点由子树hash的hash构成，因此抽象化的每一棵形态相同的merkle tree必然具有相同的root hash。那么这里的RootHash就唯一地确认了一棵merkle tree $M$。

如果一个从根到任意节点的有序集合$p$ (不一定为路径)被推断出Key/Value，称$p$是一个有效的证明。把$p$集合记为$P$，这里的Proof就被定义为
$$
_{|p|}\min \{p\in P|p\text{ proves the existence of Key/Value in merkle tree M}\}
$$
例如$$H_1, H_2, ..., H_n$$是Simple Merkle Tree $M$，从根$H_1$到Key/Value $H_n$的路径，可以知道Proof等于$H_{[2,n)}$。这样当一个人接收到:

+ Type: Simple Merkle Tree.
+ RootHash: $H_1$.
+ Proof: $H_{[2,n)}$.
+ Key/Value: $H_n$.

他能够立即由$hash (H_k) = H_{k-1}, 1<k\leqslant n$知道这个Merkle Proof是否正确。