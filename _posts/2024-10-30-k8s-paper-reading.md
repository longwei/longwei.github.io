---
layout: post
title: k8s up and runngin
---


分布式系统在互联网时代，尤其是大数据时代到来之后，成为了每个程序员的必备技能之一。分布式系统从上个世纪80年代就开始有了不少出色的研究和论文，我在这里只列举最近15年范围以内我觉得有重大影响意义的15篇论文（15 within 15）。

1. The Google File System: 这是分布式文件系统领域划时代意义的论文，文中的多副本机制、控制流与数据流隔离和追加写模式等概念几乎成为了分布式文件系统领域的标准，其影响之深远通过其5000+的引用就可见一斑了，Apache Hadoop鼎鼎大名的HDFS就是GFS的模仿之作；

2. MapReduce: Simplified Data Processing on Large Clusters：这篇也是Google的大作，通过Map和Reduce两个操作，大大简化了分布式计算的复杂度，使得任何需要的程序员都可以编写分布式计算程序，其中使用到的技术值得我们好好学习：简约而不简单！Hadoop也根据这篇论文做了一个开源的MapReduce；

3. Bigtable: A Distributed Storage System for Structured Data：Google在NoSQL领域的分布式表格系统，LSM树的最好使用范例，广泛使用到了网页索引存储、YouTube数据管理等业务，Hadoop对应的开源系统叫HBase（我在前公司任职时也开发过一个相应的系统叫BladeCube，性能较HBase有数倍提升）；

4. The Chubby lock service for loosely-coupled distributed systems：Google的分布式锁服务，基于Paxos协议，这篇文章相比于前三篇可能知道的人就少了，但是其对应的开源系统zookeeper几乎是每个后端同学都接触过，其影响力其实不亚于前三篇；

5. Finding a Needle in Haystack: Facebook's Photo Storage：facebook的在线图片存储系统，目前来看是对小文件存储的最好解决方案之一，facebook目前通过该系统存储了超过300PB的数据，一个师兄就在这个团队工作，听过很多有意思的事情（我在前公司的时候开发过一个类似的系统pallas，不仅支持副本，还支持Reed Solomon-LRC，性能也有较多优化）；

6. Windows Azure Storage: a highly available cloud storage service with strong consistency：windows azure的总体介绍文章，是一篇很好的描述云存储架构的论文，其中通过分层来同时保证可用性和一致性的思路在现实工作中也给了我很多启发；

7. GraphLab: A New Framework for Parallel Machine Learning：CMU基于图计算的分布式机器学习框架，目前已经成立了专门的商业公司，在分布式机器学习上很有两把刷子，其单机版的GraphChi在百万维度的矩阵分解都只需要2~3分钟


8. Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing：其实就是 Spark，目前这两年最流行的内存计算模式，通过RDD和lineage大大简化了分布式计算框架，通常几行scala代码就可以搞定原来上千行MapReduce代码才能搞定的问题，大有取代MapReduce的趋势；

9. Scaling Distributed Machine Learning with the Parameter Server：百度少帅李沐大作，目前大规模分布式学习各家公司主要都是使用ps，ps具备良好的可扩展性，使得大数据时代的大规模分布式学习成为可能，包括Google的深度学习模型也是通过ps训练实现，是目前最流行的分布式学习框架，豆瓣的开源系统paracell也是ps的一个实现；

10. Dremel: Interactive Analysis of Web-Scale Datasets：Google的大规模（近）实时数据分析系统，号称可以在3秒相应1PB数据的分析请求，内部使用到了查询树来优化分析速度，其开源实现为Drill，在工业界对实时数据分析也是比价有影响力；

11. Pregel: a system for large-scale graph processing: Google的大规模图计算系统，相当长一段时间是Google PageRank的主要计算系统，对开源的影响也很大（包括GraphLab和GraphChi）；

12. Spanner: Google's Globally-Distributed Database：这是第一个全球意义上的分布式数据库，Google的出品。其中介绍了很多一致性方面的设计考虑，简单起见，还采用了GPS和原子钟确保时间最大误差在20ns以内，保证了事务的时间序，同样在分布式系统方面具有很强的借鉴意义；

13. Dynamo: Amazon’s Highly Available Key-value Store：Amazon的分布式NoSQL数据库，意义相当于BigTable对于Google，于BigTable不同的是，Dynamo保证CAP中的AP，C通过vector clock做弱保证，对应的开源系统为Cassandra；

14. S4: Distributed Stream Computing Platform：Yahoo出品的流式计算系统，目前最流行的两大流式计算系统之一（另一个是storm），Yahoo的主要广告计算平台；

15. Storm @Twitter：这个系统不多说，开启了流式计算的新纪元，几乎是所有公司流式计算的首选，绝对值得关注；

最近一两年时间主要精力放到了机器学习上，分布式系统的研究不太多了，现阶段就列这15篇文章吧，覆盖了分布式系统的主要领域。如果想起来有遗漏再来补充。Good luck！


作者：三年马
链接：https://www.zhihu.com/question/615235209/answer/3214197623
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

———————————————————————-python学完异常处理，之后学一下三个主流库：numpy、matplotlib、pandas，然后去学torch，torch不要硬学，如果是cv方向去github上找minist手写字体识别、花朵分类、resnet跑分类之类的任务代码，先大体过一遍，然后debug，那个torch的函数不理解就去搜他的用法（csdn上很多）。然后去理解cnn rnn lstm transformer这些基础中的基础，现在你基础就差不多了，可以去选一些感兴趣的方向了，我拿cv举例子，先从目标检测入手去看yolo系列这种最简单的，先从理论入手早期的v1-v3，没必要去看论文，去多看几篇别人整理好的博客，看他们算法的原理，每一个版本提升的trick。然后去看v4的论文和博客，了解理论，带着理论去debug v5的源码，v5很经典。后续跳过v6 ，直接开始v7和v8。yolo通了之后可以去了解ssd（感觉没太必要）、两阶段的faster rcnn、cascade rcnn（打比赛现在还很好用）。再去看transformer，从vit开始 detr、deformable detr 等等出色的变体。这一套下来目标检测领域基本该了解的就都了解了，下面就可以去看图像分割（语义、实例）：unet系列（unet unet++ unet3+ 其实u^2net这种显著性目标检测感觉就是语义分割，医学的可以去看nnunet  ps：今年用nnunet改进打了个天池top10 医学影像看来最重要的还是数据处理方式和参数配置等 totalsegmentator医学的分割挺好用 他就是nnunet➕大量数据集跑出来的），deeplab系列（v1到v3+），然后meta今年出来的SAM、以及各种后续发展比如医学影像的medical sam adapter。maskformer系列。实例分割的mask rcnn什么的。再去看一些经典的下游任务：目标跟踪 姿态估计 行为识别 等等。想什么比较经典的deepsort openpose什么的其实总结下来都是先用一个很好用的encoder，然后用在不同的任务做不同的输出罢了注意：在路线的过程中都是要先理解这个算法的理论，最后形成一个知识体系，基本就自动汇总了，还有对应的基础知识也要大体知道怎么回事，比如各种卷积 各种归一化 各种激活函数 用于不同任务的各种损失函数，各种数据增强库和有趣的增强方式，各种特征提取网络的特点、各种特征融合然后就可以去思考一些你想要解决的问题和创新点了，一定要懂理论结合着代码，（可不是把一个源码跑通了：配置个环境，debug改改路径改改超参数，加一些即插即用的模块。要跟着源码去理解这个算法的思想）接下来也就算入门了，打打比赛提升一下对于不同任务的数据的理解和那些顶级的解决思路，提升工程能力。或者找一个方向尝试写写论文。大致就这样，很干货了，可以借鉴一下。- - - - - - - - - - - - - - - - - - - - - - - - - - - - - -看到这么多人关注这个问题，看来国家2035的人工智能发展政策，大家都想赶上这个浪潮啊 。我决定再补充一些：关注问题的可能有本科和研究生，但是本科关注这个比较深入的路线，看来也是想读研搞算法了，那就一概而论了。可能很多朋友都找好了自己的研究方向或者有感兴趣的领域，那么接下来的路无非两条：继续科研和就业。在这里提醒各位一句，如果要卷科研的话，发论文肯定是必备的，那么一个好的研究方向肯定事半功倍。传统的检测和分割等都已经被卷爆炸了，很难有冲击高水平的创新了（大牛组当我没说），简而言之，想相对简单的发高水平，方向越偏越好 做的人越少越好。意思可不是说越新的越好哦，近段时间卷起来的多模态、各种无标签的监督（半监督、自监督）什么的，这种想要选择需要你有足够硬的算力，一般玩不起的，毕竟业界sota用的集群都是什么大家了解下就有数了。其次是就业，与科研相反的是 如果是奔着就业去的，那么选的方向千万别太偏，一定要和工业界的研究方向贴合最近越好，也就是比如经典的检测的：缺陷、自动驾驶相关、或者就搞下游场景的任务。和公司岗位的方向一定要match，有这样的b会。甚至有的时候比方向较偏的顶会都管用（今年双c9的同学，都有拿着a会因为方向偏很难拿offer的情况）。就业的同学多参加一下kaggle 天池 ，没拿到名次也能提升自己，拿到好名次就血赚，其他的一些竞赛也可以：比如科大讯飞算法挑战赛（前一阵，看到讯飞的比赛进入决赛拿奖好像不错的奖励，就打了个cv的竞赛，侥幸top3，还在代码审核期，接下来如果讯飞top有用，会再跟进）、或者顶会的workshop 等等。总之，就业和科研 其实在最合适性上来说。需要这种矛盾的选择，当然有实力的同学，可以不用选择这两条路线，综合一下或者任意选择我觉得都可以。如果有什么问题，可以私信，看到会回 - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -再借一次，kaggle UBC这场cv赛想拿牌的私



