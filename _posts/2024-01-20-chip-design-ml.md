---
layout: post
title: Chip ML design
---


# ch1 Overview
when to use ML
(1) lean (2) complex patterns with (3) existing data to (4) make predictions (5) on unseen data

* repetitive: each pattern is repeated and machine learnable
* cost of prediction is cheap
* at scale to acquire benefit
* the pattern are constantly changing.

ML in Research vs. in Production
* different stakeholder
* fast inference, low latency (more swe)
* data constantly shifting
* fairness
* interpretablity

ML algo is smaller part, ML leaderboard
batch prediction
latency is important
ML vs SWE, versioning data, 怎么评估？

# ch2 ML Basic

Business and ML objectives 要综合考虑
* solve problem faster, spend less money on you
* magically: yes, but not overnight

ML system: 
* relibility, fail silently
* scalability, peak on prime day
* maintainability, DevOps, SRE
* adaptability, data distribution shift

Framing ML problem
classification:
binary: is cat?
multiclass: cat or dog, cardinality种类
multilabel: cat and dog


Objective functions, Mind vs Data




# ch3 Data Engineering:
### Row/column parquet (pandas)
OLTP vs OLAP insert vs aggregate
Dataflow not db, services but real-time transport 

### real-time transport (kakfa, kinesis): pub/sub (topic) vs message queue
computation on straming data: flink
it is stateful, only compute the delta new data and then join with old data.


# ch4 training data
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


### data augmentation



# ch5 feature engineering:

the jobs is to come up with new useful features.
problem? data leakage and how to detect and avoid it.

deep learning => feature learning, extracted automatically
The process of choosing what information to use and how to extract this information into a format usable by your ML models is feature engineering
or domain-specific tasks such as predicting whether a transaction is fraudulent, you might need subject matter expertise with banking and frauds to be able to come up with useful features.

## Common Feature Engineering Operations

### missing data:
missing not at random: the data is natually missing value
missing ar random

deletion for missing values or fill in (imputation)
column deletion remove important information that your model needs to make predictions, especially if the missing values are not at random (MNAR).
row deletion  create biases in your model
avoid filling missing values with possible values


### Scaling
Before inputting features into models, it’s important to scale them to be similar ranges
ML models tend to struggle with features that follow a skewed distribution

Note: 1. it’s a common source of data leakage. 2. often requires global statistics from training for inference


### Discretization
problem is category boundaries

### Encoding Categorical Features
categories change, not static. decide the bucket is hard.
hashing trick: The hashed value will become the index of that category, Because you can specify the hash space, you can fix the number of encoded values for a feature in advance, without having to know how many categories there will be
One problem with hashed functions is collision


### Feature Crossing
This technique is useful to model the nonlinear relationships between features
con: it can make your feature space blow up and overfitting
直接上AutoML

### Discrete and Continuous Positional Embeddings
“Attention Is All You Need”
words’ positions need to be explicitly inputted in parallel: (“a dog bites a child” is very different from “a child bites a dog”)
neural networks don’t work well with inputs that aren’t unit-variance (that’s why we scale our features
A way to handle position embeddings is to treat it the way we’d treat word embedding, 

## Data Leakage
models were “found to be picking up on the text font that certain hospitals used
a phenomenon when a form of the label “leaks” into the set of features used for making predictions, and this same information is not available during inference.

cause:
Splitting time-correlated data randomly instead of by time


## Detecting Data Leakage
Do ablation studies,if removing a feature causes the model’s performance to deteriorate significantly, investigate why that feature is so important.
subject matter expertise
Be very careful every time you look at the test split (不能漏题)


## Engineering Good Features

Having too many features can be bad both during training and serving
In theory, if a feature doesn’t help a model make good predictions, regularization techniques like L1 regularization should reduce that feature’s weight to 0。 or removed, prioritizing good features.

### Feature Importance
XGBoost =>  importance of your features
SHapley Additive exPlanations, is great because it not only measures a feature’s importance to an entire model, it also measures each feature’s contribution to a model’s specific prediction


### generalization to unseen data
Measuring feature generalization is a lot less scientific than measuring feature importance, and it requires both intuition and subject matter expertise on top of statistical knowledge.

### Summary

* Split data by time into train/valid/test splits instead of doing it randomly.
* If you oversample your data, do it after splitting.
* Scale and normalize your data after splitting to avoid data leakage.
* Use statistics from only the train split, instead of the entire data, to scale your features and handle missing values.
* Understand how your data is generated, collected, and processed. Involve domain experts if possible.
* Keep track of your data’s lineage.
* Understand feature importance to your model.
* Use features that generalize well.
* Remove no longer useful features from your models.


# ch6 model development
When selecting a model for your problem, you don’t choose from every possible model out there, but usually focus on a set of models suitable for your problem.

detect fraudulent transactions, you know that this is the classic abnormality detection problem—fraudulent transactions are abnormalities that you want to detect—and common algorithms for this problem are many, including k-nearest neighbors, isolation forest, clustering, and neural networks.


### tips
avoid state of the art and start with the simplest
aviud human bias in selecting models
evaluate good performace now vs. later
evaluate trade off, false positives and false negatives trade-off
understand model's assumption.



### ensemble
bagging: boostrapping, aggregating
boosting: enhance the signal
stacking: meta-learning

### distributed taining
data parallelism: 
model paralleisam. 



### Experiment Tracking and Versioning

An artifact is a file generated during an experiment—examples of artifacts can be files that show the loss curve, evaluation loss graph, logs, or intermediate results of a model throughout a training process. 
data is often much larger than code, we can’t use the same strategy that people usually use to version code to version data.

### Distributed Training
data parallelism vs Pipeline parallelism

### AutoML
Hyperparameter tuning
Hard AutoML: Architecture search and learned optimizer

### evaluation
permutation test vs noise: 主动加噪音
invariance test vs certain change won't change output 
directional expection test: some change should cause predictable change in output

vs baseline. 去production找





### ch7 prediction servie
online prediction: faud detection, time sensitive.
batch prediction: recommender system


### model compression:
low rank facorization: convolutional filter
knowledge disillation: smaller representation
pruning: remove low signal
quantization: down-size long -> int


## ch 8 monitoring: data distribution shifts ...

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

# ch 9
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

