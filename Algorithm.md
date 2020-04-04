# 图论
> ### 图的遍历
- DFS：深度优先搜索
    - 有向图：
        - 树边：遍历形成的树上的边
        - 回边：其他所有的边
    - 无向图：
        - 树边：遍历形成的树上的边
        - 回边：树上子节点到父节点的边
        - 前向边：树上父节点到子节点的边
        - 横跨边：其他树子节点之间的边
    - predfn：前序遍历标号
    - postdfn：后序遍历标号
- DAG：有向无环图
    - DAG <=> 有向图的DFS中有回边
    - 拓扑排序（在序列中任意u在v前面，如果在图中存在边，那么图中u也在v前面）：
        1. 算法一：为所有入度0的节点添加共同根节点，由此开始DFS，postdfn降序即为拓扑序
        2. 算法二：不断寻找source节点，直到图为空
    - DAG中每条边都指向一个postdfn更小的点
    - 在深度优先搜索中，无环<=>可线性化<=>无回边
    - DAG中至少有一个source/一个sink，它们没有入度/出度

- BFS：广度优先搜索
    - Dijkstra：最短路径算法（存在负边权时，不满足前提：任何通过其它节点中转的路径都比直接选中当前节点距离要远）
    - Bellman-Ford：含负边的最短路径算法，复杂度O(|V||E|)
        1. 初始化距离为无穷大，从某一节点0开始
        2. 对所有上一轮更新过的节点，更新它的出边节点
        3. 重复执行|V|-1轮，一定达到稳定
        - 如果|V|-1轮后未达到稳定，说明存在负环
    - DAG中的单源最短路径：按照拓扑序列执行更新操作（不要求边权为正）
        - 对所有边权取反，可以求出最长路径
> ### SCC 强连通分支
- 有向图的强连通分支：任意两节点之间存在往返路径可达，满足该条件的极大集
- 任意有向图的SCC形成一个DAG
- 从sink节点出发DFS，得到一定是SCC
- SCC形成的的DAG拓扑序列，与SCC中最大节点的拓扑序列一致
- 算法：
    1. 在G上DFS
    2. 颠倒G中所有边的方向，构成G'
    3. 从G'中的sink节点（也即G中source节点）开始，在G'中DFS，找到SCC后删去，继续重复第三步

# NP 问题
> ### 有效算法
- 把指数级搜索空间缩减到多项式时间可完成
- 最小生成树：选取边能够连接所有节点的最小花费
    - Kruskal：贪婪地从所有边中选取最小权重的边，且不形成回路
    - Prim：从一个顶点出发，不断选取最小权重的边来扩张集合
- 2-SAT（合取范式）：为每个变量x构造两个点x和~x，对每个x v y构造边~x->y 和 ~y->x。如果存在强连通分量同时包含x 和 ~x，那么不可满足。
- 容易的问题：多项式时间可解
- 困难的问题：多项式时间不太可能解
- 判定问题：回答是不是，存不存在
- 最优化问题：回答最大值，最小值
- 判定问题和最优化问题可以相互转化
> ### P 问题：可以使用确定性算法在多项式时间内求解的问题
- 2-Coloring
- 2-SAT
- P问题在补运算下是封闭的
> ### NP 问题：可以使用确定性算法在多项式时间内验证的问题
- Coloring
> ### NP-Complete 问题：是一种NP，并且所有的NP可以规约到NPC问题(NP-hard不一定是NP)
- 可满足问题：第一个NPC问题
- Hamiltonian Cycle：访问每个顶点恰好一次
- Traveling Salesman:是否存在一个回路距离小于k
- Clique:有最大为k的团(完全子图)
- Vertex Cover:大小为k的点集覆盖了所有的边
- Independence Set:大小为k的点集内两两无边
- 3-SAT  
- 3-Coloring
- 3-DimensionalMatching
- HamiltonianPath
- LongestPath
- Partition
> ### co-NP:补问题是NP的问题
- co-NP complete:是co-NP问题,且每个co-NP都能归约到该问题
- NP完全 <=> 补问题是co-NP完全
- 重言式问题属于P <=> co-NP=P
- 重言式问题属于NP <=> co-NP=NP
- co-NP!=NP => NP!=P
> ### NPI:不确定是P还是NP
- NPI = co-NP∩NP
- 素数

# 数论
> ### Bit复杂度
- 数字的进制不会影响O运算下的复杂度
> ### Addition
- 注意到任意三个单bit数字之和不会超过两个bit,因此任意n bits两个数相加之和最多为n+1 bits
- 算法复杂度O(n)
> ### Multiplication
- T(n) = 3T(n/2) + O(n)
- $O(n^{log_23}) = O(n^{1.59})$
> ### Modular
- Modular Addition:O(n)
- Modular Multiplication:O(n2)
- Modular Exponentiation:O(n3)
> ### Euclid’s Algorithm for Greatest Common Divisor
> ### Modular Inverse
- 最多有一个,并且可能不存在
> ### Fermat’s Little Theorem
- 如果p是质数,那么$a^{p-1}\equiv1(mod p)...(1\le a \lt p)$
- 卡迈克尔数可以通过素数测试,但不是素数
