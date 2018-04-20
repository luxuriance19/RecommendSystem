<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default">
# RecommendSystem
Recommend System Implementation
### 基于邻域的方法：协同过滤
用户协同过滤  
物品协同过滤  
User-co-filtering and ItemCF：推荐算法的评价标准实现，UserCF和ItemCF的实现  

### 隐形语义模型： Latent factor model(LFM)  
在LFM当中，分类来自于对用户行为的统计，并且对物品的类采用软分类，也就是权重的方式来表达，当指定的类别数目多的时候代表分类的粒度越细。其中的分类可以是
不同维度的。

关键问题： 怎么样给用户生成负样本（因为数据里面只包含正样本，也就是用户喜欢什么样的物品）  
负样本采样原则：  
1、对每个用户，保证正负样本数量平衡；  
2、对每个用户采样负样本时，要选择热门的，但是用户却没有行为的物品。（因为如果说时冷门的物品，不一定用户不喜欢，只是没有发现而已，但是热门的，但是用户却没有行为，也就是表现出不喜欢）。

LFM最大的缺点是需要训练出用户的隐类向量$p_{u}$和物品的隐类向量$q_{i}$，这个需要通过从用户行为记录上面反复迭代梯度下降算法而得到。  
实际应用当中LFM每天智能训练一次，计算出所有用户的推荐结果。所以LFM不能实时变化来调整推荐结果满足用户最近的行为。在新闻当中，冷启动时因为新用户必须要有用户行为之后，通过离线计算的用户相似度表计算之后才能够有相关推荐。这里冷启动问题也比较明显。  
解决方案：  
1、利用新闻链接的内容属性（关键次、类别）得到链接i的内容$y_{i}$，实时收集用户的链接行为，利用这些数据得到链接i的隐含特征向量$q_{i}$.  
2、利用如下公式预测用户u是否会单机链接i：  


$$ r_{ui} = x_{u}^{T}y_{i} + p_{u}^{T}q_{i}$$  
$x_{u}$是用户向量，根据用户的历史行为记录获得，一天只需要计算一次，$x_{uk}$是用户u对内容特征k的兴趣程度，$y_{i}$是根据物品的内容属性直接生成的。  

### LFM和CF方法比较：
1、LFM有学习的过程，CF没有。  
2、离线计算的空间复杂度CF明显高于LFM。    
3、离线计算的时间复杂度两者差不多，LFM略高于CF。    
4、在线实时推荐LFM不是很合适，例如ItemCF如果用户有了新的行为，她的推荐列表就会发生变化，但是LFM必须要重新计算用户对所有物品的兴趣的权重，然后排名。
所以LFM不适合物品数目非常庞大的系统， 用户行为变更之后，不会马上改变推荐列表。   
5、ItemCF的解释性比LFM解释性好。

### 基于图的模型：（基于邻域的方法可以看成简单模型）  
相关性高的一对顶点一般具有如下特征：  
1、两个顶点之间有很多路径相连；
2、两个顶点之间的路径长度都比较短；  
3、两个顶点之间的路径不会经过出度比较大的顶点。（两个顶点之间的路径经过的顶点，出度是这个顶点的边的数目）

基于随机游走的PersonalRank算法：  
给用户u进行i个性化推荐，用户u可以从对应的节点$v_{u}$开始在用户物品二分图上进行随机游走。 游走到任意一个节点，首先按照概率$\alpha$决定是否继续游走，
还是停止这次游走，重新从$v_{u}$开始游走。 如果决定继续游走，就从当前节点只想的节点当中按照均匀分布随机选择一个节点作为游走下次经过的节点。这样， 经过很多次随机游走之后，每个物品节点访问的概率会收敛到一个数，最终的推荐列表种物品的权重就是物品节点的访问概率。（类似于马尔科夫状态度，最终收敛）。

$$ PR(v) = \begin{cases} \alpha \sum_{v^{'} \in (v) \alpha \frac{PR(v^{'}}{out(v^{'}} (v \neq v_{u}) \\ (1-\alpha) + \alpha  \frac{PR(v^{'}}{out(v^{'}} (v \eq v_{u}) $$
</script>
