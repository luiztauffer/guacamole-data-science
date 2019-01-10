---
layout: single
title:  "Pump it up - part 2"
date:   2019-01-08 

categories: 
    - projects
tags: 
    - machine learning
    - random forests

class: wide

excerpt: "predictive modelling"

header:
  overlay_image: /assets/images/pump-it-up-main.png
  overlay_filter: 0.5
  teaser: /assets/images/pump-it-up-main.png
  og_image: /assets/images/pump-it-up-main.png
  
---

# Predicting water pumps failures

We will keep working on the [**Pump it up**](https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/) challenge. 


# Modelling with Random Forests

We're gonna use the amazing Random Forests!



# Decision trees for water pump maintenance prediction
Fit a Distributed Random Forests classifier using H2O

http://docs.h2o.ai/h2o/latest-stable/h2o-py/docs/modeling.html#h2orandomforestestimator


```python
#%%capture
import h2o

# Start H2O on your local machine
h2o.init(nthreads=1)
```

    Checking whether there is an H2O instance running at http://localhost:54321. connected.
    Warning: Your H2O cluster version is too old (9 months and 19 days)! Please download and install the latest version from http://h2o.ai/download/
    

```python
# Load pandas DataFrames into H2O dataframes
XY = pd.concat([X_train,Y_train],axis=1)
hf = h2o.H2OFrame(XY)

nObs = hf.shape[0]
nVal = int(nObs*.2)
nTrain = nObs - nVal

hf_train = hf[0:nTrain,:]
hf_val = hf[nTrain:,:]
hf_test = h2o.H2OFrame(X_test)

```

    Parse progress: |█████████████████████████████████████████████████████████| 100%
    Parse progress: |█████████████████████████████████████████████████████████| 100%
    


```python
from h2o.estimators.random_forest import H2ORandomForestEstimator

rfc = H2ORandomForestEstimator(nfolds=10, ntrees=20, max_depth=7, balance_classes=False, build_tree_one_node=True)
rfc.train(y='status_group', training_frame=hf_train, validation_frame=hf_val)
```

    drf Model Build progress: |███████████████████████████████████████████████| 100%
    


```python
#Confusion Matrices
print('Confusion Matrix for Training set')
print( rfc.confusion_matrix(hf_train) )

print('Confusion Matrix for Validation set')
print( rfc.confusion_matrix(hf_val) )

```

    Confusion Matrix for Training set
    Confusion Matrix: Row labels: Actual class; Column labels: Predicted class
    
    


<div style="overflow:auto"><table style="width:50%"><tr><td><b>functional</b></td>
<td><b>functional needs repair</b></td>
<td><b>non functional</b></td>
<td><b>Error</b></td>
<td><b>Rate</b></td></tr>
<tr><td>24096.0</td>
<td>18.0</td>
<td>1016.0</td>
<td>0.0411460</td>
<td>1,034 / 25,130</td></tr>
<tr><td>2704.0</td>
<td>106.0</td>
<td>356.0</td>
<td>0.9665193</td>
<td>3,060 / 3,166</td></tr>
<tr><td>7577.0</td>
<td>22.0</td>
<td>10176.0</td>
<td>0.4275105</td>
<td>7,599 / 17,775</td></tr>
<tr><td>34377.0</td>
<td>146.0</td>
<td>11548.0</td>
<td>0.2538039</td>
<td>11,693 / 46,071</td></tr></table></div>


    
    Confusion Matrix for Validation set
    Confusion Matrix: Row labels: Actual class; Column labels: Predicted class
    
    


<div style="overflow:auto"><table style="width:50%"><tr><td><b>functional</b></td>
<td><b>functional needs repair</b></td>
<td><b>non functional</b></td>
<td><b>Error</b></td>
<td><b>Rate</b></td></tr>
<tr><td>5917.0</td>
<td>5.0</td>
<td>337.0</td>
<td>0.0546413</td>
<td>342 / 6,259</td></tr>
<tr><td>664.0</td>
<td>18.0</td>
<td>83.0</td>
<td>0.9764706</td>
<td>747 / 765</td></tr>
<tr><td>1956.0</td>
<td>14.0</td>
<td>2523.0</td>
<td>0.4384598</td>
<td>1,970 / 4,493</td></tr>
<tr><td>8537.0</td>
<td>37.0</td>
<td>2943.0</td>
<td>0.2656074</td>
<td>3,059 / 11,517</td></tr></table></div>


    
    


```python
#from h2o.estimators.gbm import H2OGradientBoostingEstimator
#gbm_regressor = H2OGradientBoostingEstimator(distribution="multinomial", ntrees=30, max_depth=5, min_rows=2, learn_rate=0.2)
#gbm_regressor.train(y='status_group', training_frame=hf)
```
