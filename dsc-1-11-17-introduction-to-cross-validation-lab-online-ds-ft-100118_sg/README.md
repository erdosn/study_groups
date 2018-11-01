
# Introduction to Cross-Validation - Lab

## Introduction

In this lab, you'll be able to practice your cross-validation skills!


## Objectives

You will be able to:

- Compare the results with normal holdout validation
- Apply 5-fold cross validation for regression

## Let's get started

This time, let's only include the variables that were previously selected using recursive feature elimination. We included the code to preprocess below.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
from sklearn.datasets import load_boston
from sklearn.linear_model import LinearRegression

boston = load_boston()

boston_features = pd.DataFrame(boston.data, columns = boston.feature_names)
b = boston_features["B"]
logdis = np.log(boston_features["DIS"])
loglstat = np.log(boston_features["LSTAT"])

# minmax scaling
boston_features["B"] = (b-min(b))/(max(b)-min(b))
boston_features["DIS"] = (logdis-min(logdis))/(max(logdis)-min(logdis))

#standardization
boston_features["LSTAT"] = (loglstat-np.mean(loglstat))/np.sqrt(np.var(loglstat))

# adding target to boston_features data for kfolds coming up later
boston_features["target"] = boston.target
```


```python
X = boston_features[['CHAS', 'RM', 'DIS', 'B', 'LSTAT']]
y = boston.target
```

## Train test split

Perform a train-test-split with a test set of 0.20.


```python
from sklearn.model_selection import train_test_split
```


```python
xtrain, xtest, ytrain, ytest = train_test_split(X, y, test_size=0.20)
```

Fit the model and apply the model to the make test set predictions


```python
model = LinearRegression()
model.fit(xtrain, ytrain)
```




    LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)




```python
def mse(ypred, ytrue):
    """
    formula = 1/n * sum(fi-yi); where fi = model_value, yi=true_value
    input
    ypred: array of predictions
    ytrue: array of true values (with respect to our ypred)
    
    output
    mse: float
    """
    N = ypred.shape[0]
    # print(N)
    return float(np.sum(np.square(ypred-ytrue)))/N
```

Calculate the residuals and the mean squared error


```python
yhat_train = model.predict(xtrain)
yhat_test  = model.predict(xtest)

# residuals
train_residuals = yhat_train - ytrain
test_residuals  = yhat_test  - ytest

# mse error
mse_train = mse(yhat_train, ytrain)
mse_test  = mse(yhat_test,  ytest)

# print results
print(mse_train, mse_test) # mean squared error
print(np.sqrt(mse_train), np.sqrt(mse_test)) # root mean squared error
```

    20.674460248887016 25.888097770631557
    4.546917664625896 5.088034765076941


## Cross-Validation: let's build it from scratch!

### Create a cross-validation function

Write a function k-folds that splits a dataset into k evenly sized pieces.
If the full dataset is not divisible by k, make the first few folds one larger then later ones.

We want the folds to be a list of subsets of data!


```python
def kfolds(data, k):
    # Force data as pandas dataframe
    # add 1 to fold size to account for leftovers
    data = pd.DataFrame(data) # dataframe
    num_observations = len(data) # number of rows
    fold_size = num_observations//k # floor division 3.82 -> 3
    leftovers = num_observations%k # remainder after division by k
    folds = []
    start_obs = 0
    for fold_n in range(1,k+1): # loop from 1, 2, ..., k
        if fold_n <= leftovers:
            # Fold Size will be 1 larger to account for leftovers
            fold =  data.iloc[start_obs : start_obs+fold_size+1] # slicing rows from 0 to k+1
            folds.append(fold)
            start_obs +=  fold_size + 1 # setting start to fold_size + 1
        else:
            fold =  data.iloc[start_obs : start_obs+fold_size] 
            folds.append(fold)
            start_obs +=  fold_size
            
    return folds 
    return None
```

### Apply it to the Boston Housing Data


```python
# Make sure to concatenate the data again
# folding on boston_features data since it also includes target values
folds = kfolds(boston_features, 5)
```


```python
# notice this includes target
folds[0].head() 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>CRIM</th>
      <th>ZN</th>
      <th>INDUS</th>
      <th>CHAS</th>
      <th>NOX</th>
      <th>RM</th>
      <th>AGE</th>
      <th>DIS</th>
      <th>RAD</th>
      <th>TAX</th>
      <th>PTRATIO</th>
      <th>B</th>
      <th>LSTAT</th>
      <th>target</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.00632</td>
      <td>18.0</td>
      <td>2.31</td>
      <td>0.0</td>
      <td>0.538</td>
      <td>6.575</td>
      <td>65.2</td>
      <td>0.542096</td>
      <td>1.0</td>
      <td>296.0</td>
      <td>15.3</td>
      <td>1.000000</td>
      <td>-1.275260</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.02731</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>6.421</td>
      <td>78.9</td>
      <td>0.623954</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>1.000000</td>
      <td>-0.263711</td>
      <td>21.6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.02729</td>
      <td>0.0</td>
      <td>7.07</td>
      <td>0.0</td>
      <td>0.469</td>
      <td>7.185</td>
      <td>61.1</td>
      <td>0.623954</td>
      <td>2.0</td>
      <td>242.0</td>
      <td>17.8</td>
      <td>0.989737</td>
      <td>-1.627858</td>
      <td>34.7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.03237</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>6.998</td>
      <td>45.8</td>
      <td>0.707895</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>0.994276</td>
      <td>-2.153192</td>
      <td>33.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.06905</td>
      <td>0.0</td>
      <td>2.18</td>
      <td>0.0</td>
      <td>0.458</td>
      <td>7.147</td>
      <td>54.2</td>
      <td>0.707895</td>
      <td>3.0</td>
      <td>222.0</td>
      <td>18.7</td>
      <td>1.000000</td>
      <td>-1.162114</td>
      <td>36.2</td>
    </tr>
  </tbody>
</table>
</div>



### Perform a linear regression for each fold, and calculate the training and test error

Perform linear regression on each and calculate the training and test error.


```python
test_errs = []
train_errs = []
k=5

for n in range(k):
    # Split in train and test for the fold
    train_set = pd.concat([fold for i, fold in enumerate(folds) if i!=n]) # train folds are folds!=n
    test_set = folds[n] # current fold is test fold
    
    # split X and y data
    xtr = train_set.loc[:, train_set.columns!='target'] # xtrain
    ytr = train_set.target # ytrain
    xte = test_set.loc[:, test_set.columns!='target'] # xtest
    yte = test_set.target #ytest
    # print(ytr.shape, yte.shape, train_set.shape, test_set.shape) # use if you want to check that the shapes match
    
    # init/fit linreg
    linreg = LinearRegression() # optional you can also just use 'model' 
                                # from above to fit the current model on a new data set
    linreg.fit(xtr, ytr)
    
    # get yhats for train and test
    yhat_tr = linreg.predict(xtr)
    yhat_te = linreg.predict(xte)
    
    # calculate residuals
    tr_res = yhat_tr - ytr
    te_res = yhat_te - yte
    
    # append the mean sq err to their respective lists
    train_errs.append(np.mean(tr_res.astype(float)**2)) # mean sqr err appended to list
    test_errs.append(np.mean(te_res.astype(float)**2))  # mean sqr err appended to list

    
print(train_errs)
print(test_errs)
```

    [17.91857867030196, 17.361583277713574, 15.543427264673552, 11.040378388195787, 17.23404426556593]
    [13.04498602423651, 14.64079170968883, 24.8580642761322, 55.300233948565, 19.218460110193845]


## Cross-Validation using Scikit-Learn

This was a bit of work! Now, let's perform 5-fold cross-validation to get the mean squared error through scikit-learn. Let's have a look at the five individual MSEs and explain what's going on.


```python
from sklearn.metrics import mean_squared_error # calcualte mean_squared_error
from sklearn.model_selection import cross_val_score # will calculate cross_val_score same as for loop
```

Next, calculate the mean of the MSE over the 5 cross-validations and compare and contrast with the result from the train-test-split case.


```python
# cross validating X and y with 5 folds read the docstring below if you want cv and scoring explained
cv_5_results = cross_val_score(linreg, X, y, cv=5, scoring="neg_mean_squared_error") # cv = number of folds, scoring = #type of score
```


```python
cv_5_results # Notice this is similar to test_errs above, except negative
```




    array([-13.40514492, -17.4440168 , -37.03271139, -58.27954385,
           -26.09798876])




```python
# Docstring for cross_val_score
"""
Evaluate a score by cross-validation

Read more in the :ref:`User Guide <cross_validation>`.

Parameters
----------
estimator : estimator object implementing 'fit'
    The object to use to fit the data.

X : array-like
    The data to fit. Can be for example a list, or an array.

y : array-like, optional, default: None
    The target variable to try to predict in the case of
    supervised learning.

groups : array-like, with shape (n_samples,), optional
    Group labels for the samples used while splitting the dataset into
    train/test set.

scoring : string, callable or None, optional, default: None
    A string (see model evaluation documentation) or
    a scorer callable object / function with signature
    ``scorer(estimator, X, y)``.

cv : int, cross-validation generator or an iterable, optional
    Determines the cross-validation splitting strategy.
    Possible inputs for cv are:

    - None, to use the default 3-fold cross validation,
    - integer, to specify the number of folds in a `(Stratified)KFold`,
    - An object to be used as a cross-validation generator.
    - An iterable yielding train, test splits.

    For integer/None inputs, if the estimator is a classifier and ``y`` is
    either binary or multiclass, :class:`StratifiedKFold` is used. In all
    other cases, :class:`KFold` is used.

    Refer :ref:`User Guide <cross_validation>` for the various
    cross-validation strategies that can be used here.

n_jobs : integer, optional
    The number of CPUs to use to do the computation. -1 means
    'all CPUs'.

verbose : integer, optional
    The verbosity level.

fit_params : dict, optional
    Parameters to pass to the fit method of the estimator.

pre_dispatch : int, or string, optional
    Controls the number of jobs that get dispatched during parallel
    execution. Reducing this number can be useful to avoid an
    explosion of memory consumption when more jobs get dispatched
    than CPUs can process. This parameter can be:

        - None, in which case all the jobs are immediately
          created and spawned. Use this for lightweight and
          fast-running jobs, to avoid delays due to on-demand
          spawning of the jobs

        - An int, giving the exact number of total jobs that are
          spawned

        - A string, giving an expression as a function of n_jobs,
          as in '2*n_jobs'

Returns
-------
scores : array of float, shape=(len(list(cv)),)
    Array of scores of the estimator for each run of the cross validation.
"""
```

##  Summary 

Congratulations! You now practiced your knowledge on k-fold crossvalidation!
