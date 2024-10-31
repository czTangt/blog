# Lexer Re Automata


这里记录词法分析，正则表达式，自动机的相关知识。

&lt;!--more--&gt;

## 正则表达式与自动机理论
### 课程目标
这里讲解怎么写一个自动化词法分析器生成器。根据前面的理论，我们使用 ANTLR4 来**生成词法分析器**，其实质上是我们使用 ANTLR4 利用正则表达式 (regular expression -&gt; RE) 的规则来进行生成词法分析器。同时我们还学习了利用 java 来**手写词法分析器**，实质就是在使用 java 代码模拟状态转移图，它也就是自动机。那么我们来看 ANTLR4 原理，他就是把 `.g4` 文件转化为 `.java` 文件，也就是把正则表达式转化为了自动机，然后通过模拟自动机就可以得到词法分析器了。
因此我们的目标就是通过正则表达式来直接得到得到一个词法分析器。

![conversion](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410201955612.png &#34;compile&#34;)

由上图，我们构建词法分析器就是把 RE 转化为 DFA（有穷状态自动机 Deterministic FInite Automata），然后再转化为词法分析器，但是这个过程往往是困难的，所以我们采用简略的方法，通过先转化为 NFA（不确定的又穷状态自动机Nondeteeministic Finite Automata），再转化为 DFA，再进行后续的操作。

### 编程语言介绍
**语言是字符串构成的集合。** 根据这句高度抽象的结论，我们会一层层进行解剖。
- 字符
	- 字母表 $\Sigma$ 是一个有限的符号集合，符号没有意义，它的语义是后来自己赋予的。
- 字符串
	- 字符表 $\Sigma$ 上的**串(s)** 是由 $\Sigma$ 中符号构成的一个**有穷**序列。
	- 其中 $\epsilon$ 是空串，我们定义它为零，即 $|\epsilon| = 0$ 
- 字符串之间存在运算
	- **连接**运算， $x = day, y = houce, xy = dayhouce, \epsilon s = s \epsilon = s$
	- **指数**运算，$s\^{0} \triangleq \epsilon$，$s\^{i} \triangleq ss\^{i-1}, i&gt;0$，这里存在上标就是连接的意思
- 语言
	- 语言是给定字母表 $\Sigma$ 上一个任意的**可数**的串集合。
	- $\varnothing$，这一个是空集，什么语言都没有；$\{ \epsilon \}$，这个里面有一个语言，不过是个空串
	- 举例：id：$\{a,b,c,d,a1\}$；ws：$\{blank, tab, newline \}$，if：$\{ if \}$
	- 我们知道语言是串的集合，正因为是集合，所以我们可以通过集合操作**构造**新的语言

![rules](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410202146130.png &#34;compile&#34;)

## RE
每个正则表达式 r 对应一个正则语言 **L(r)**。正则表达式是**语法**（ID：`[a-zA-Z][a-zA-Z0-9]*`），正则语言是**语义**（`{a1,a2,ab,……}`）

### 语法
给定字母表，$\Sigma$ 上的正则表达式由且仅由以下规则定义：
1. $\epsilon$ 是正则表达式
2. $\forall a \in \Sigma$，a 是正则表达式
3. 如果 r 是正则表达式，则 (r) 是正则表达式
4. 如果 r 与 s 是正则表达式，则 r | s，rs，r* 也是正则表达式
5. 运算优先级：$() \succ * \succ 连接 \succ |$ ，例子：$(a) \mid ((b)\^{\*} (c)) \equiv a | b\^{\*} c$


### 语义
正则表达式对应的正则语言 $L(r)$
1. $L(\epsilon) = \{ \epsilon\}$
2. $L(a) = a, \forall a \in \Sigma$
3. $L((r)) = L(r)$
4. $L(r|s)=L(r) \cup L(s)\quad L(rs)=L(r)L(s)\quad L(r\^{\*})=(L(r))\^{\*}$

### 符号

![symbol](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410202205123.png &#34;compile&#34;)

![symbol](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410202206620.png &#34;compile&#34;)

## 自动机
### NFA
#### 语法

非确定性有穷自动机 $\mathcal{A}$  是一个五元组 $\mathcal{A} = (\Sigma, S, s_0, \delta, F)$ 
1. 字母表 $\Sigma (\epsilon \notin \Sigma)$
2. 有穷的**状态集合** $S$
3. **唯一**的初始状态 $s_0 \in S$，这里的唯一不是强求，因为可以转化为唯一形态，转化方法就是前面再添加ige初始状态，然后通过 $\{\epsilon \}$ 边转移到原始初始状态即可。
4. **状态转移函数** $\delta$，$\delta: S \times (\Sigma \cup \{\epsilon\}) \rightarrow 2\^S$
5. 接受状态集合 $F \subseteq S$，下图的 3 就是接受状态

- 这里非确定一个就是指接受统一字符的状态转移不唯一，如下图的 0 号节点，它接受字符 a 可以跑到两个状态上去；另一个就是可能存在 $\{ \epsilon \}$ 边，在没有字符驱动的情况下自发的跑到另外一个状态。

![state transfer](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410202223241.png &#34;compile&#34;)

上面的状态转移图没有规定如果碰到其他的字符该怎么处理，所以下图就约定所有没有对应出边的字符默认指向 空状态 $\varnothing$，也就是  $(\Sigma \cup \{\epsilon\})$，它表示达到自身，也意味着一个死状态。 

![state transfer](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410202251440.png &#34;compile&#34;)

#### 语义
有穷自动机是一类及其简单的计算装置，它可以识别（接收/拒绝）$\Sigma$ 上的字符串
- 接收
	- (非确定性)有穷自动机 $\mathcal{A}$ 接受字符串 x，当且仅当存在一条从开始状态 $s_0$ 到**某个**接受状态 $f \in F$ 、标号为 x 的路径。
	- 对于上面的状态转移图，只有 3 是接受状态，因此 $aabb \in L(\mathcal{a}), ababab \notin L(\mathcal{A})$
	- 因此，$\mathcal{A}$ 定义了一种语言 $L(\mathcal{A})$：它能接受的所有字符串构成的集合。所以根据上方状态转移图，可以得到自动机语言：$L(\mathcal{A}) = L((a|b)^*abb)$
由上面的语义，我们可以得到自动机的两个基本问题
- Membership 问题：给定字符串 $x$，$x \in L(\mathcal{A})?$
- $L(\mathcal{A})$ 究竟是什么？

### DFA
#### 语法
确定性有穷自动机 $\mathcal{A}$  是一个五元组 $\mathcal{A} = (\Sigma, S, s_0, \delta, F)$ 
1. 字母表 $\Sigma (\epsilon \notin \Sigma)$
2. 有穷的**状态集合** $S$
3. **唯一**的初始状态 $s_0 \in S$，这个唯一是一定需要的
4. **状态转移函数** $\delta$，$\delta: S \times \Sigma \rightarrow S$
5. 接受状态集合 $F \subseteq S$，下图的 3 就是接受状态

![state transfer](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410202317792.png &#34;compile&#34;)

这里的约定就是：所有没有对应出边的字符串默认指向一个“**死状态**”

#### 语义
上图的自动机语言还是 $L(\mathcal{A}) = L((a|b)^*abb)$，也就是上面的 NFA 和下面的 DFA 等价的。因此可以看出 NFA 适合去表达一个语言，容易得出语言是什么；而 DFA 则是因为状态的转移确定，适合写词法分析器。即 NFA 简介易于理解，便于描述语言 $L(\mathcal{A})$；DFA易于判断$x \in L(\mathcal{A})$，适合产生词法分析器。那么转换就是 $RE \Rightarrow NFA \Rightarrow DFA \Rightarrow$ 词法分析器。  

## 相互转换
这里就是根据下面这张图，使得正则表达式和自动机之间相互转换。

![conversion](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410201955612.png &#34;compile&#34;)

### RE -&gt; NFA
采用 Thompson 构造法，使得 $r \Rightarrow NFA$，要求 $L(N(r)) = L(r)$，即两个语言等价。这里就是对于正则表达式语法的每个规则来定义自动机，然后最后将这些自动机按规则进行组合就得到了 NFA。
$N(r)$ 的性质以及 Thompson 构造法复杂度分析
1. $N(r)$ 的开始状态和接受状态均唯一
2. 开始状态没有入边，接受状态没有出边
3. $N(r)$ 的状态数 $|S| &lt; 2 \times |r|$（$|r|: r$ 中运算符和运算分量的总和） 
4. 每个状态最多有两个 $\epsilon \text{-}$ 入边与两个 $\epsilon \text{-}$ 出边 
5. $\forall a \in \Sigma$，每个状态最多有一个 $a \text{-}$ 入边与一个 $a\text{-}$ 出边
自动机构造如下：

![Thompson](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410210950686.png &#34;compile&#34;)

### NFA -&gt; DFA

#### 原理
采用子集构造法，也就是用 DFA 模拟 NFA。

![子集构造法](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410210952811.png &#34;compile&#34;)

下面就是从 NFA 到 DFA 的构造对应表，有了这张表就有了自动机。之所以是子集构造法，是因为构造出来的 DFA 对应于 NFA 的一个状态子集。同时这里因为在 NFA 中 10 是接受状态，所以在 DFA 中，对应的 E 也是接收状态。

![构造对应表](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410210959201.png &#34;compile&#34;)

#### 形式化描述子集构造法
这里根据上图的转化，会得到两个重要的公式：
- $\epsilon$ 闭包：从状态 s 开始，只通过 $\epsilon \text{-}$ 转移可达的状态集合
	- $\epsilon\text{-closure}(s)=\{t\in S_N|s\xrightarrow{\epsilon\^\*}t\}$，这个公式的含义就是把 NFA 中的初始状态归结于 DFA 中的初始状态。上图中 NFA 的 $\{0,1,2,4,7\}$，它是初始状态，在 NFA 中，从 0 开始，通过 $\epsilon$ 边进行连接的状态在 DFA 中都是初始状态。之后进行扩展操作 $\epsilon \text{-closure(T)} = \bigcup_{s \in T}\epsilon \text{-closure(s)}$，这个就是把上面的初始状态都添加在一起，转化为了集合形式，即状态集合，它为下面的 move 公式提供操作变量。
	- $\text{move(T,a)} = \bigcup_{s\in T} \delta(s,a)$，这个公式就是根据集合的当前状态，然后根据转移函数 $\delta$，逐个查看集合中每个元素在**同一个字符**作用的目标元素是什么，最后将目标元素添加到新集合中，这个集合就是 DFA 中的下一个状态。 
之后就可以形式化描述子集构造法：子集构造法($N \rightarrow D$) 的**原理**：
$$
\begin{array}{l}
N: (\Sigma_N, S_N, n_0, \delta_N, F_N) \\\\
D: (\Sigma_D, S_D, d_0, \delta_D, F_D) \\\\
\Sigma_{D} = \Sigma_{N} \\\\
S_{D} \subseteq 2^{S_{N}} \quad (\forall s_{D} \in S_{D} : s_{D} \subseteq S_{N})
\end{array}
$$
- 初始状态：$d_{0} = \epsilon \text{-closure}(n_{0})$
- 状态转移：$\forall a \in \Sigma_{D} : \delta_{D}(s_{D}, a) = \epsilon\text{-closure}(\operatorname{move}(s_{D}, a))$
- 接受状态集：$F_{\mathcal{D}} = \{ s_{D} \in S_{\mathcal{D}} \mid \exists f \in F_N \colon f \in s_{D} \}$
子集构造法的复杂度分析：（$|S_N=n|$，下面的符号就是算法分析中的分析符号）
$$\left|S_{D}\right| = \Theta\left(2^{n}\right) = O\left(2^{n}\right) \cap \Omega\left(2^{n}\right)$$
**对于任何算法**，最坏情况下，$|S_{D}| = \Omega\left(2^{n}\right)$。

### DFA最小化
#### 方法
我们还是查看之前使用 NFA -&gt; DFA 的转换图来看，下面的 DFA 就是使用子集构造法将 NFA 转化而来的，毫无疑问，与上面的图相比，它不是最小的，所以这里需要探究的就是如何将 DFA 转化为最小化的形式。

![conversion](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410211050216.png &#34;compile&#34;)

这里**DFA最小化算法**基本思想：**等价**的状态可以合并。对于等价而言，如果存在某个能区分状态 s 与 t 的字符串，则称 s 与 t 是**可区分的**；否则，称 s 与 t 是**等价的**。这里的字符串 x 区分状态 s 与 t，就是指如果分别从 s 与 t 出发，沿着标号为 x 的路径到达的两个状态中只有一个是接受状态，则称**区分**了状态 s 与 t，也就是s 与 t 不等价。
所以状态等价就是说，对于两个状态而言，在**任意同一个字符**的驱动下从当前状态进行转换，转换后的状态也是等价的。它可以用下面的公式进行表示：
$$
\begin{array}{l}
s \sim t \iff \forall a \in \Sigma. \left( (s \xrightarrow{a} s&#39;) \land (t \xrightarrow{a} t&#39;) \implies (s&#39; \sim t&#39;) \right)\\\\
s \nsim t \iff \exists a \in \Sigma. \left( (s \xrightarrow{a} s&#39;) \land (t \xrightarrow{a} t&#39;) \land (s&#39; \nsim t&#39;) \right)
\end{array}
$$
基于该定义，不断**合并**等价的状态，直到无法合并为止。但是我们的定义是一个递归的，不知道一开始要从什么地方入手，同时我们又得到所有的接受状态并不是等价的。所以这里采取的办法就是划分，利用反例公式 $s \nsim t \Longleftrightarrow \exists a \in \Sigma. ( s \xrightarrow{a} s&#39; ) \land ( t \xrightarrow{a} t&#39; ) \land ( s&#39; \nsim t&#39; )$ 进行划分，而非合并。也就是首先根据接受状态与非接受状态必定不等价先划分为两类 $\Pi = \{F, S \setminus F\}$，然后在这个基础上根据上面的反例公式进行分裂，直至再也无法**划分**为止，这里就到达了**不动点**，之后就是将同一等价类里的状态**合并**。

![划分步骤](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410211200842.png &#34;compile&#34;)

上面就是分裂的过程，在 $\Pi_0$ 到 $\Pi_1$ 的过程中，$\{A,B,C\}$ 和 $\{D\}$ 在经过 b 进行传递的状态是不等价的，此时 D 转移到 E 上了，E 输出 $S \setminus F$，所以不等价。之后的操作也是这样挑选字符看转移后的状态处于哪一个集合中，如果不在本身的集合，那么就是不等价，需要进行分裂。

![合并](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410211203861.png &#34;compile&#34;)

上图就是最后的分裂之后再合并得到最小化 DFA 的转换。

#### 注意
- 需要注意处理&#34;**死状态**&#34;，也就是指向 $\{ \varnothing\}$ 的一些没有画出来的边，在进行分裂时需要添加上，即$\{F, S \setminus F, \{ \varnothing \}\}$
- 刚刚的算法不适用于 NFA 最小化，NFA最小化问题是 PSPACE-complete 的，复杂度很高。

### DFA -&gt; 词法分析器
这里对于词法分析器的构造，需要注意一下几个要求，然后按照之前使用 java 模拟的方法进行构造即可：
- 需要满足**最前优先匹配**和**最长优先匹配**，与此同时，因为需要生成词法分析器的特定目的，所以要保留各个 NFA 的**接受状态**的信息，表明匹配的是什么正则表达式
- 需要**消除** &#34;死状态&#34;，避免词法分析器徒劳消耗输入流。如果加上死状态，那么词法分析器就有可能走这条路径，然后会进行一直匹配，最后匹配出的也是死状态，妨碍正确匹配。
- 进行模拟的过程如下图所示，和之前 java 模拟的过程一样。

![模拟过程](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410211302285.png &#34;compile&#34;)

- 最后需要注意初始划分需要考虑不同的**词法单元**。之前的划分按照接受状态和非接受状态进行划分，但是这里需要写词法分析器，所以最后的接收状态对应了不同的词法单元，所以也需要进一步划分为不同的集合。

![特定词法单元](https://nuthecz.oss-cn-hangzhou.aliyuncs.com/file/202410211304789.png &#34;compile&#34;)

---

> 作者: [czTang](https://github.com/czTangt)  
> URL: https://czTangt.github.io/blog/categories/compile/lexer-re-automata/  

