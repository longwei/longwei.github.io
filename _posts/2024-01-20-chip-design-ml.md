---
layout: post
title: Chip ML design
---


## Data:
### Row/column parquet (pandas)
OLTP vs OLAP insert vs aggregate
Dataflow not db, services but real-time transport 

### real-time transport (kakfa, kinesis): pub/sub (topic) vs message queue
computation on straming data: flink
it is stateful, only compute the delta new data and then join with old data.

### How to handle data from BA's view?
sampling logic
simple random sampling
stratifield sampling, define the group
weighted sampling
reservoir sampling
importance sampling

### labeled hand vs natural

weak supervision: heuristics
transfer learning
active learning: label the sample the least concern


### class imbalance
insufficient signal to learn to detect the minority classes
use the right evaluation metrics. ROC curve
data level method: resampling: x oversampling: SMOTE
algorithem-level method: 


## feature engineering:
### missing data:
missing not at random: the data is natually missing value
missing ar random

deletion for missing values. imputation



## model development

### tips
avoid state of the art and start with the simplest
aviud human bias in selecting models
evaluate good performace now vs. later
evaluate trade off
understand model's assumption.


### ensemble
bagging: boostrapping, aggregating
boosting: enhance the signal
stacking: meta-learning

### distributed taining
data parallelism: 
model paralleisam. 

vs baseline.

### evaluation
permutation test vs noise:
invariance test vs certain change won't change output
directional expection test: some change should cause predictable change in output


### prediction servie
online prediction: faud detection, time sensitive.
batch prediction: recommender system


### model compression:
low rank facorization: convolutional filter
knowledge disillation: smaller representation
pruning: remove low signal
quantization: down-size long -> int


## monitoring: data distribution shifts ...

software system failure: deps/deploy/hw failure
ML specific failure: production data diff, edges case, degenerate feedback loop 

### data distribution shift
sovariate shift: p(x) change but P(Y|X) remain the same, age goes up.
label shift: p(y) change but p(x|y) remain the same.
concept shift: P(Y|X) change but P(x) remain the same

### detect distribution shift
statistical method or time scale window to detect the shift.

### address distribution shift
use massive dataset
adapt a trained model to target distribution without requiring new label
retrain the model (most common)


### ML sepecific metrics
raw input, features, prediction, accuracy-related metrics


## continual learning and testing in production
continual learning to update ML model, it is mostly infra problem.
stateful training.

### continual learning challenges
fresh data access challenge
evaluation challenge
algorithem challenge



###
https://zh.d2l.ai/
学习机器学习的方法
https://www.zhihu.com/question/639864582

李航老师的《统计学习方法第二版》

个人观点，学习机器学习的方法其他回答全都过时了，没必要学统计的东西。直接看李沐或者李宏毅的视频，或者直接看博客。把mlp，cnn，rnn，transformer这些基本结构了解一下。然后直接读比较新的顶会的有开源代码的论文。然后把它的代码搞懂，直接用gpt，让她给你讲。然后自己再尝试去缝缝模块改改结构。这样一套流程下来就入门了。你要真想学统计的东西，李航的机器学习方法比周的好。同样的内容周3页，李的30页。b站有李航第二版统计学习方法的配套的详细公式推导。是一位江苏的统计青椒老师做的。更新:回答的时候忘记了简老师的名字了，经评论区提醒，直接在b站搜"统计学习方法简博士"，讲的非常详细清晰。

作者：王庆东
链接：https://www.zhihu.com/question/639864582/answer/3367180527
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





## feature engineering.

1. access: access to feature info, transparency, lineage
2. serving: availability in production at high throughput and low lantency. the user don't need to sql retrieval from data warehouse. integration with offline storage(s3) with online storage(redis). real-time feature transformation.
3. integrity: minimize train-serve skew, point-in-time correct data. 以确保历史特征和标签被用于训练和评估时不存在 data leaks
4. convenience: easy quick to use. interactivity
5. autopilot: automated backfill and alerts, feature selection.

