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


















