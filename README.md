<div id="article_content" class="article_content csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
                    <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/htmledit_views-0a60691e80.css">
            <div class="htmledit_views">
                
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
这个案例跟推荐系统相关，预测用户可能感兴趣的event。关于这个案例更多信息打开<a target="_blank" href="https://www.kaggle.com/c/event-recommendation-engine-challenge/data" style="color:rgb(12,137,207)">event_recommendation_competition</a>。这里我直接讲解第一名的解决方案。<span style="">这个方案中除了包含经典的机器学习解决步骤，还融合了推荐系统里传统的解决方法：基于用户的协同过滤，基于物品的协同过滤，当然也可以融合LFM模型等等</span>，因为这个解决方案很经典，所以我觉得值得拿出来详细讲讲。我将贴出完整代码，并且进行很详细的讲解。</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
首先看看比赛给的数据：train,test,users,events,user_friends,attendees这六张表。详细的数据描述请打开上面链接查看。</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<div style="color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px"><br style="">
train表：&nbsp;<br style="">
<img src="https://img-blog.csdn.net/20170625155906871?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:100%; max-height:100%"></div>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<div style="color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px"><br style="">
test表：&nbsp;<br style="">
<img src="https://img-blog.csdn.net/20170625160055065?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:100%; max-height:100%"></div>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<div style="color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px"><br style="">
users表：&nbsp;<br style="">
<img src="https://img-blog.csdn.net/20170625160144218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:100%; max-height:100%"></div>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<div style="color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px"><br style="">
events表：&nbsp;<br style="">
<img src="https://img-blog.csdn.net/20170625163443411?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:100%; max-height:100%"></div>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<div style="color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px"><br style="">
event_attendees表：&nbsp;<br style="">
<img src="https://img-blog.csdn.net/20170625160233499?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:100%; max-height:100%"></div>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<div style="color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px"><br style="">
user_friends表：&nbsp;<br style="">
<img src="https://img-blog.csdn.net/20170625160356532?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:100%; max-height:100%"></div>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
</p>
<ul style="line-height:27.2px; margin-bottom:1.7em; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
<li style="">首先这是一个 推荐 的问题</li><li style="">
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px">
我们有下面这样几类数据&nbsp;<br style="">
①：用户的历史数据 =&gt; 对 event 是否感兴趣/是否参加&nbsp;<br style="">
②：用户社交数据 =&gt; 朋友圈&nbsp;<br style="">
③：event 相关的数据 =&gt; event</p>
</li><li style="">
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px">
简单思考&nbsp;<br style="">
①：要把更多维度的信息纳入考量。&nbsp;<br style="">
②：协同过滤是基于user -event 历史交互数据。&nbsp;<br style="">
③：需要把社交数据和event 相关信息 作为影响最后结果的因素纳入考量。&nbsp;<br style="">
④：视作分类模型，每一个人感兴趣/不感兴趣 是 target，其他影响结果的是feature。&nbsp;<br style="">
⑤：<span style="">影响结果的 feature 包括由协同过滤产出的推荐度</span>。（userCF，itemCF）</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px">
<span style="">第一名的解决方案思路：</span></p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px">
<img src="https://img-blog.csdn.net/20170625162135884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTXJfdHl0aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:none; margin-top:15px; margin-bottom:15px; max-width:602px; max-height:100%; height:auto">&nbsp;<br style="">
因为数据很大，用pandas读取数据，内存可能吃不下，所以我们用scipy.sparse里面稀疏矩阵dok_matrix来一行一行的存储数据。&nbsp;<br style="">

1.数据清洗类&nbsp;<br style="">
这个类主要做一些数据预处理的事情，例如对user性别进行0,1化；对locale进行编号；将字符串型的日期转成date型；等等。</p>

<span style="">2.处理user和event关联数据</span>&nbsp;<br style="">
这个类主要用来统计以下几个数据：&nbsp;<br style="">
①：uniqueUsers :set(),统计train和test中不同的user。&nbsp;<br style="">
②：uniqueEvents :set(),统计train和test中不同的event。&nbsp;<br style="">
③：eventsForUser :{event:set(users)},统计train和test中各个event有多少个不同user对其有交互。&nbsp;<br style="">
④：usersForEvent:{user:set(events)},统计train,test中各个user对多少个不同的event产生交互。&nbsp;<br style="">
⑤：userEventScores :dok_matrix形式，统计train中各个user对各个event感兴趣程度。shape[len(uniqueUsers ,uniqueEvents )]&nbsp;<br style="">
⑥：uniqueUserPairs :set(),train,test中对同一个event感兴趣的users的两两组合。&nbsp;<br style="">
⑦：uniqueEventPairs ：set(),train,test中被同一个user感兴趣的events两两组合。&nbsp;<br style="">
⑧：userIndex：dict(),{user,i},uniqueUsers 中的user与其对应的序号。&nbsp;<br style="">
⑨：eventIndex：dict(),{event,i},uniqueEvents中的event与其对应的序号。</p>

 
<span style="">3.用户与用户相似度矩阵</span>&nbsp;<br style="">
这个类主要统计以下几个数据：&nbsp;<br style="">
①userMatrix :dok_matrix形式，根据users表，shape[len(users),len(users.columns)-1],除去users表中的user_id列，将表中其他列数据放进这个矩阵。就是统计userIndex表中各个user在users表中的属性。&nbsp;<br style="">
②userSimMatrix ：dok_matrix形式，shape[len(users),len(users)],根据userMatrix矩阵中用户属性数据，利用scipy.spatial.distance.correlation(欧式距离）来计算uniqueUserPairs中每两个user（<span style="">这两个user对至少对同一个events发生过行为</span>）相似度，从而得出用户相似度矩阵。这种相似度计算类似于协同过滤里相似度。</p>


<span style="">4.用户社交关系挖掘</span>&nbsp;<br style="">
这个类分析用户社交信息，统计以下几个数据：&nbsp;<br style="">
①：numFriends ：矩阵，shape[1,len(users)],统计userIndex表中每个user有多少个friends。如果用户朋友很多在一定程度上说明用户性格外向。&nbsp;<br style="">
②：userFriends ：dok_matrix,shape[len(users),len(users)],统计第i个user的第j个朋友的活跃程度。如果用户的朋友都很活跃能在一定程度上说明用户也很活跃。</p>


<span style="">5.构造event和event相似度数据</span>&nbsp;<br style="">
<span style="">构建event-event相似度，注意这里有2种相似度：</span>&nbsp;<br style="">
①eventPropMatrix ：dok_matrix形式，shape[len(events),7]，查看events表会发现，有109列，其中前9列是<span style="">event与user历史交互信息</span>，除去event_id,user_id两列，将剩余的每列数据放进eventPropMatrix 矩阵中。&nbsp;<br style="">
②eventContMatrix:dok_matrix形式，shape[len(events),100]，events表中剩余的100列表示event本身内容信息，其中count_N是表示第N个最常见词干在该事件的名称或描述中出现的次数的整数。 count_other是其余词的计数。将events中剩余的9-109列数据放进该矩阵中 。&nbsp;<br style="">
③eventPropSim: dok_matrix形式，shape[len(events),len(events)]，event相似度矩阵，根据eventPropMatrix 矩阵中<span style="">event-user历史交互信息</span>，再利用scipy.spatial.distance.correlation计算uniqueEventPairs中每两个event(这两个event至少被同一个user产生过行为)相似度，类似于协同过滤中相似度计算。（利用历史行为数据）&nbsp;<br style="">
④eventContSim :dok_matrix形式，shape[len(events),len(events)]，根据event本身所包含的信息计算uniqueEventPairs中每两个event(这两个event至少被同一个user产生过行为)相似度。
构建event-event相似度，注意这里有2种相似度：
  1）由用户-event行为，类似协同过滤算出的相似度
  2）由event本身的内容(event信息)计算出的event-event相似度</p>


<span style="">6.活跃度/event热度 数据</span>&nbsp;<br style="">
eventPopularity :dok_matrix形式，shape[len(events),1)],将event表中yes列和no列数据作差作为其event活跃度。</p>


<span style="">7.串起所有的数据处理和准备流程</span></p>

<span style="">8.构建特征（DataRewriter类）</span>&nbsp;<br style="">
将上述保存到本地一些统计数据读取出来。</p>

<span style="">基于用户协同过滤推荐结果作为特征</span>，我们以计算给用户i对活动j感兴趣程度为例，userSimMatrix[i, :]表示用户i与所有用户的相似度，userEventScores[：,j]表示所有用户对活动j的感兴趣程度。以上两矩阵相乘表示基于其他用户对活动j的感兴趣程度上计算用户i对活动j的感兴趣程度，这就是基于用户协同过滤推荐。</p>

<span style="">基于event协同过滤推荐结果作为特征</span>，这里event相似度有两种，一个是基于user-event历史交互信息得出的相似度，一种是基于event本身的内容信息得出的相似度。同上面类似的计算出推荐信息作为特征。</p>

<span style="">生成训练集特征，测试集特征</span>&nbsp;<br style="">
生成以下特征：&nbsp;<br style="">
①：train中invited特征&nbsp;<br style="">
②：基于用户的推荐结果信息&nbsp;<br style="">
③：基于event的推荐结果信息&nbsp;<br style="">
④：用户活跃程度（根据朋友数量）&nbsp;<br style="">
⑤：用户受朋友影响程度&nbsp;<br style="">
⑥：活动的流行程度&nbsp;<br style="">
如果是train：&nbsp;<br style="">
⑦：interest&nbsp;<br style="">
⑧：not interest</p>

<span style="">9.建模与预测</span>&nbsp;<br style="">
实际上在上述特征构造好了之后，我们有很多的办法去训练得到模型和完成预测，这里用了sklearn中的SGDClassifier 事实上xgboost有更好的效果（显然我们的特征大多是密集型的浮点数，很适合GBDT这样的模型）&nbsp;<br style="">
注意交叉验证，我们这里用了10折的交叉验证</p>
<p style="margin-top:0px; margin-bottom:1.7em; padding-top:0px; padding-bottom:0px; line-height:27.2px; color:rgb(63,63,63); font-family:&quot;microsoft yahei&quot;; font-size:16px">
用训练集特征训练模型</p>

  在我们得到的特征上训练分类器，target为1(感兴趣)，或者是0(不感兴趣)
我们再用学习曲线看看是否过拟合还是前拟合</p>


