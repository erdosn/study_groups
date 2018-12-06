
# K-Nearest Neighbors - Lab

## Introduction

In this lesson, we'll build a simple version of a **_K-Nearest Neigbors Classifier_** from scratch, and train it to make predictions on a dataset!

## Objectives

You will be able to:

* Implement a basic KNN algorithm from scratch

## What we learned

* I didn't use enumerate....?
* Learned that this is possible without enumerate
* The ins and outs of instantiating a machine learning model, Chris
* What KNN is and how it calcualtes NN by distance, Chris
* Taking the hood off of KNN, Danyal
* Euc distance and that mathematicians murder their students...yikes..., Emily

## Getting Started

We'll begin this lab by creating our classifier.  To keep things simple, we'll be using a helper function from the scipy library to calcluate euclidean distance for us--specifically, the `euclidean()` function from the `scipy.spatial.distance` module. Import this function in the cell below.


```python
from scipy.spatial.distance import euclidean as euc # pythagathorean theorem
import numpy as np
np.random.seed(0)
```

Great! Now, we'll need to define our `KNN` class. Since we don't need to do anything at initialization, we don't need to modify the `__init__` method at all.

In the cell below:

* Create an class called `KNN`.
* This class should contain two empty methods--`fit`, and `predict`. (Set the body of both of these methods to `pass`)


```python
class KNN(object):
    
    def __init__(self):
        self.X_train=None
        self.y_train=None
        self.distance=distance
        pass
    
    
    def fit(self):
        pass
    
    def predict(self):
        pass
```

## Completing the `fit` Method

Recall from our previous lesson on KNN that when "fitting" a KNN classifier, all we're really doing is storing the points and their corresponding labels. There's no actual "fitting" involved here, since all we can do is store the data so that we can use it to calculate the nearest nighbors when the `predict` method is called.

Our inputs for this function should be:

* `self`, since this will be an instance method inside the `KNN` class.
* `X_train`--A 2-dimensional array. Each of the internal arrays represents a _vector_ for a given point in space. 
* `y_train`--the corresponding labels for each vector in `X_train`. The label at `y_train[0]` is the label that corresponds to the vector at `X_train[0]`, and so on. 

In the cell below, complete the `fit` method.


```python
def fit(self, X_train, y_train):
    self.X_train = X_train
    self.y_train = y_train
    data = np.column_stack([X_train, y_train])
    return data
    
# This line updates the knn.fit method to point to the function we've just written
KNN.fit = fit
```

### Helper Functions

Next, we'll write two helper functions to make things easier for us when completing the `predict` function. The first helper function we'll write return an array containing the distance between a point we pass in and every point inside of `X_train`. 

In the cell below, complete the `_get_distances()` function. This function should:

* Take in two arguments: `self` and `x`
* Create an empty array to hold all the distances we're going to calculate
* Enumerate through every item in `self.X_train`. For each item: 
    * Use the `euc()` function we imported to get the distance between x and the current point from `X_train`.
    * Create a tuple containing the index and the distance (in that order!) and append it to our `distances` array.
* Return the `distances` array when a distance has been generated for each item in `self.X_train`.


```python
def _get_distances(self, x):
    # x is a point
    distances = []
    index = 0
    for point in self.X_train:
        distance = euc(x, point)
        distance_store = (index, distance)
        index += 1
        distances.append(distance_store)
    distances = np.array(distances)
    return distances

# This line attaches the function we just created as a method to our KNN class.
KNN._get_distances = _get_distances

# (0, distance0)
# (1, distance1)
# (2, distance2)
# (3, distance3) ...
```

Great! The second big challenge in a `predict` method is getting the indices of the k-nearest points. To keep our coming `predict` method nice and clean, we'll abstract this functionality into a helper method called `_get_k_nearest`.  

In the cell below, complete the `_get_k_nearest` function.  This function should:

* Take in 3 arguments:
    * `self`
    * `dists`, an array of tuples containing (index, distance), which will be output from the `_get_distances` method. 
    * `k`, the number of nearest neighbors we want to return.
* Sort our `dists` array by distances values, which are the second element in each tuple
* Return the first `k` tuples from then (now sorted) `dists` array.

**_Hint:_** To easily sort on the second item in the tuples contained within the `dists` array, use the `sorted` function and pass in lambda for the `key=` parameter. To sort on the second element of each tuple, we can just use `key=lambda x: x[1]`!


```python
# sort by distance
# (i0, distanceclosest0)
# (i1, distanceclosest1)
# (i2, distanceclosest2)

def _get_k_nearest(self, dists, k):
    k_nearest = sorted(dists, key=lambda x:x[1], reverse=False)[:k]
    return k_nearest
    pass

# This line attaches the function we just created as a method to our KNN class.
KNN._get_k_nearest = _get_k_nearest
```

Now, we have helper functions to help us get the distances, and then get the k-nearest neighbors based on those distances. The final helper function we'll create will help us get the labels that correspond to each of the k-nearest point, and return the class that occurs the most. 

Complete the `_get_label_prediction()` function in the cell below. This function should:

* Create a list containing the labels from `self.y_train` for each index in `k_nearest` (remember, each item in `k_nearest` is a tuple, and the index is stored as the first item in each tuple)
* Get the total counts for each label (use `np.bincount()` and pass in the label array created in the previous step)
* Get the index of the label with the highest overall count in counts (use `np.argmax()` for this, and pass in the counts created in the previous step).


```python
def _get_label_prediction(self, k_nearest):
    indices = [int(i[0]) for i in k_nearest]
    labels = np.bincount(self.y_train[indices]) # counting different labels 
    return np.argmax(labels)

# This line attaches the function we just created as a method to our KNN class.
KNN._get_label_prediction = _get_label_prediction
```


```python
label_sample = np.array([0, 1, 1, 1, 0, 2, 2, 0, 1])
```


```python
np.bincount(label_sample)
```




    array([3, 4, 2])




```python
np.argmax(np.bincount(label_sample))
```




    1



Great! Now, we need to complete the `predict` method. This will be much simpler, now that we have some 

## Completing the `predict` Method

This method does all the heavy lifting for KNN, so this will be a bit more complex than our `fit` method. Let's examine how this method should work, so that we'll have a better idea of how to write it.

1. The function takes in an array of vectors that we want predictions for.
1. For each vector that we want to make a prediction for: 
    1a. The classifier calculates the distance between that vector and every other vector in our training set. 
    1b. The classifier identifies the K nearest vectors to the vector we want a prediction for.
    1c. The classifier determines which label the majority of the K nearest neighbors share, and appends this prediction to an array we will output. The index of the prediction in this array should be the same as the index of the point that it corresponds to (e.g. `pred[0]` is the prediction for `X_test[0]`).
2. Once predictions have been generated for every vector in question, return the array of predictions. 

This tells us a few things about what our `predict` function will need to be able to do:

* In addition to `self`, our `predict` function should take in two arguments: 
    * `X_test`, the points we want to classify
    * `k`, which specifies the number of neighbors we should use to make the classification.  We'll set `k=3` as a default, but allow the user to update it if they choose.
* Our method will need to iterate through every item in `X_test`. For each item:
    * Calculate the distance to all points in `X_train` by using our `_get_distances()` helper method we created.
    * Find the k-nearest points in `X_train` by using the `_get_k_nearest()` helper method we created
    * Use the index values contained within the tuples returned by `_get_k_nearest()` to get the corresponding labels for each of the nearest points. 
    * Determine which class is most represented in these labels and treat that as the prediction for this point. Append the prediction to `preds`.
* Once a prediction has been generated for every item in `X_test`, return `preds`

Follow these instructions to complete the `predict()` method in the cell below!


```python
def predict(self, X_test, k=3):
    # calculate the distance between X_test[0] and all of our train vectors
    predictions = np.zeros(shape=(X_test.shape[0], 1))
    index = 0
    for point in X_test:
        distances = self._get_distances(point)
        k_nearest = self._get_k_nearest(distances, k)
        prediction = self._get_label_prediction(k_nearest)
        predictions[index] = prediction
        index += 1
    return predictions
        
KNN.predict = predict
```

Great! Now, let's try out our new KNN classifier on a sample dataset to see how well it works!

## Testing Our KNN Classifier

In order to test the performance of our model, we're going to import the **_Iris Dataset_**. Specifically, we'll use the `load_iris` function, which can be found inside of the `sklearn.datasets` module. We'll then call this function, and use the object it returns. We'll also import `train_test_split` from `sklearn.model_selection`, as well as `accuracy_score` from `sklearn.metrics`.  Note that there are **_3 classes_** in the Iris Dataset, making this a multicategorical classification problem. This means that we can't use evaluation metrics that are meant for binary classification problems. For this, we'll just stick to accuracy. 

Run the cell below to import everything we'll need from sklearn to test our model. 


```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

iris = load_iris()
data = iris.data
target = iris.target
```

Now, you'll need to use `train_test_split` to split our training data into training and testing sets. Pass in the `data`, the `target`, and set a `test_size` of `0.25`.


```python
X_train, X_test, y_train, y_test = train_test_split(data, target, test_size = 0.20)
```

Now, instantiate a knn object, and `fit` it to the data in `X_train` and the labels in `y_train`.


```python
knn = KNN(distance='manhattan')
```


```python
data = knn.fit(X_train, y_train)
```


```python
data
```




    array([[6.9, 3.1, 4.9, 1.5, 1. ],
           [7.9, 3.8, 6.4, 2. , 2. ],
           [5.7, 2.5, 5. , 2. , 2. ],
           [5.1, 2.5, 3. , 1.1, 1. ],
           [4.8, 3.1, 1.6, 0.2, 0. ],
           [6.3, 3.4, 5.6, 2.4, 2. ],
           [5.6, 3. , 4.5, 1.5, 1. ],
           [6.3, 2.8, 5.1, 1.5, 2. ],
           [5. , 3.3, 1.4, 0.2, 0. ],
           [6. , 2.2, 4. , 1. , 1. ],
           [6.4, 2.9, 4.3, 1.3, 1. ],
           [5.1, 3.5, 1.4, 0.3, 0. ],
           [4.9, 2.5, 4.5, 1.7, 2. ],
           [4.4, 2.9, 1.4, 0.2, 0. ],
           [7.7, 3. , 6.1, 2.3, 2. ],
           [5.5, 2.4, 3.8, 1.1, 1. ],
           [7.3, 2.9, 6.3, 1.8, 2. ],
           [5.5, 2.6, 4.4, 1.2, 1. ],
           [5.1, 3.5, 1.4, 0.2, 0. ],
           [5.5, 3.5, 1.3, 0.2, 0. ],
           [6.8, 3. , 5.5, 2.1, 2. ],
           [5.4, 3.9, 1.7, 0.4, 0. ],
           [4.9, 2.4, 3.3, 1. , 1. ],
           [7.1, 3. , 5.9, 2.1, 2. ],
           [5.7, 2.8, 4.5, 1.3, 1. ],
           [4.9, 3.1, 1.5, 0.1, 0. ],
           [6.4, 2.8, 5.6, 2.1, 2. ],
           [5.5, 4.2, 1.4, 0.2, 0. ],
           [5.1, 3.7, 1.5, 0.4, 0. ],
           [6.1, 2.8, 4.7, 1.2, 1. ],
           [5. , 3.4, 1.5, 0.2, 0. ],
           [4.8, 3. , 1.4, 0.3, 0. ],
           [7.2, 3. , 5.8, 1.6, 2. ],
           [6.3, 2.9, 5.6, 1.8, 2. ],
           [6.3, 2.5, 5. , 1.9, 2. ],
           [6.9, 3.2, 5.7, 2.3, 2. ],
           [5.6, 2.7, 4.2, 1.3, 1. ],
           [7. , 3.2, 4.7, 1.4, 1. ],
           [6.1, 2.6, 5.6, 1.4, 2. ],
           [5.7, 2.8, 4.1, 1.3, 1. ],
           [6.2, 2.8, 4.8, 1.8, 2. ],
           [5.8, 2.8, 5.1, 2.4, 2. ],
           [4.9, 3.1, 1.5, 0.1, 0. ],
           [5.1, 3.4, 1.5, 0.2, 0. ],
           [6.2, 2.9, 4.3, 1.3, 1. ],
           [5.8, 2.7, 5.1, 1.9, 2. ],
           [4.7, 3.2, 1.6, 0.2, 0. ],
           [5.5, 2.4, 3.7, 1. , 1. ],
           [5.4, 3.4, 1.7, 0.2, 0. ],
           [5.1, 3.8, 1.6, 0.2, 0. ],
           [6.4, 3.2, 4.5, 1.5, 1. ],
           [5.5, 2.3, 4. , 1.3, 1. ],
           [5.1, 3.3, 1.7, 0.5, 0. ],
           [5.2, 3.5, 1.5, 0.2, 0. ],
           [4.7, 3.2, 1.3, 0.2, 0. ],
           [5.2, 3.4, 1.4, 0.2, 0. ],
           [4.9, 3.1, 1.5, 0.1, 0. ],
           [6.4, 2.7, 5.3, 1.9, 2. ],
           [5.4, 3.7, 1.5, 0.2, 0. ],
           [5.4, 3. , 4.5, 1.5, 1. ],
           [6.4, 3.1, 5.5, 1.8, 2. ],
           [6.1, 3. , 4.9, 1.8, 2. ],
           [5. , 3.5, 1.6, 0.6, 0. ],
           [6.3, 2.3, 4.4, 1.3, 1. ],
           [5.6, 2.5, 3.9, 1.1, 1. ],
           [6.7, 3.3, 5.7, 2.5, 2. ],
           [6.7, 3.1, 5.6, 2.4, 2. ],
           [5. , 3.2, 1.2, 0.2, 0. ],
           [6.8, 2.8, 4.8, 1.4, 1. ],
           [4.6, 3.1, 1.5, 0.2, 0. ],
           [5.8, 2.7, 3.9, 1.2, 1. ],
           [6.7, 3. , 5.2, 2.3, 2. ],
           [6.5, 3. , 5.5, 1.8, 2. ],
           [5.6, 3. , 4.1, 1.3, 1. ],
           [5.1, 3.8, 1.9, 0.4, 0. ],
           [6.5, 3. , 5.2, 2. , 2. ],
           [4.9, 3. , 1.4, 0.2, 0. ],
           [5. , 2.3, 3.3, 1. , 1. ],
           [4.4, 3. , 1.3, 0.2, 0. ],
           [4.8, 3.4, 1.6, 0.2, 0. ],
           [6.4, 3.2, 5.3, 2.3, 2. ],
           [6.5, 2.8, 4.6, 1.5, 1. ],
           [5. , 3.5, 1.3, 0.3, 0. ],
           [5.7, 3.8, 1.7, 0.3, 0. ],
           [4.5, 2.3, 1.3, 0.3, 0. ],
           [5.7, 2.6, 3.5, 1. , 1. ],
           [4.8, 3.4, 1.9, 0.2, 0. ],
           [6.3, 3.3, 4.7, 1.6, 1. ],
           [6.1, 2.8, 4. , 1.3, 1. ],
           [4.3, 3. , 1.1, 0.1, 0. ],
           [5.4, 3.4, 1.5, 0.4, 0. ],
           [6. , 3.4, 4.5, 1.6, 1. ],
           [5.9, 3.2, 4.8, 1.8, 1. ],
           [6.4, 2.8, 5.6, 2.2, 2. ],
           [7.2, 3.2, 6. , 1.8, 2. ],
           [6.3, 2.7, 4.9, 1.8, 2. ],
           [6.3, 3.3, 6. , 2.5, 2. ],
           [5.2, 4.1, 1.5, 0.1, 0. ],
           [6.5, 3. , 5.8, 2.2, 2. ],
           [6. , 2.7, 5.1, 1.6, 1. ],
           [7.7, 3.8, 6.7, 2.2, 2. ],
           [7.7, 2.6, 6.9, 2.3, 2. ],
           [6. , 3. , 4.8, 1.8, 2. ],
           [5. , 3. , 1.6, 0.2, 0. ],
           [6.5, 3.2, 5.1, 2. , 2. ],
           [5.4, 3.9, 1.3, 0.4, 0. ],
           [6.6, 3. , 4.4, 1.4, 1. ],
           [7.2, 3.6, 6.1, 2.5, 2. ],
           [5.6, 2.8, 4.9, 2. , 2. ],
           [6.7, 3.1, 4.7, 1.5, 1. ],
           [6.9, 3.1, 5.4, 2.1, 2. ],
           [5. , 3.6, 1.4, 0.2, 0. ],
           [5.7, 2.9, 4.2, 1.3, 1. ],
           [5.8, 4. , 1.2, 0.2, 0. ],
           [5.9, 3. , 4.2, 1.5, 1. ],
           [5.8, 2.7, 4.1, 1. , 1. ],
           [5.9, 3. , 5.1, 1.8, 2. ],
           [5.7, 3. , 4.2, 1.2, 1. ],
           [5.1, 3.8, 1.5, 0.3, 0. ],
           [6.3, 2.5, 4.9, 1.5, 1. ]])




```python
x = X_test[0]
x
```




    array([5.8, 2.6, 4. , 1.2])




```python
dist = knn._get_distances(x)
dist
```




    array([[  0.        ,   1.53622915],
           [  1.        ,   3.5       ],
           [  2.        ,   1.28840987],
           [  3.        ,   1.22882057],
           [  4.        ,   2.83019434],
           [  5.        ,   2.21133444],
           [  6.        ,   0.73484692],
           [  7.        ,   1.26095202],
           [  8.        ,   2.9816103 ],
           [  9.        ,   0.48989795],
           [ 10.        ,   0.74161985],
           [ 11.        ,   2.97825452],
           [ 12.        ,   1.14891253],
           [ 13.        ,   3.13209195],
           [ 14.        ,   3.06431069],
           [ 15.        ,   0.42426407],
           [ 16.        ,   2.82665881],
           [ 17.        ,   0.5       ],
           [ 18.        ,   3.00998339],
           [ 19.        ,   3.03150128],
           [ 20.        ,   2.05426386],
           [ 21.        ,   2.78926514],
           [ 22.        ,   1.17473401],
           [ 23.        ,   2.50399681],
           [ 24.        ,   0.55677644],
           [ 25.        ,   2.9189039 ],
           [ 26.        ,   1.94164878],
           [ 27.        ,   3.22645316],
           [ 28.        ,   2.93087018],
           [ 29.        ,   0.78740079],
           [ 30.        ,   2.92061637],
           [ 31.        ,   2.95465734],
           [ 32.        ,   2.34946802],
           [ 33.        ,   1.80554701],
           [ 34.        ,   1.32287566],
           [ 35.        ,   2.38117618],
           [ 36.        ,   0.31622777],
           [ 37.        ,   1.52643375],
           [ 38.        ,   1.64012195],
           [ 39.        ,   0.26457513],
           [ 40.        ,   1.09544512],
           [ 41.        ,   1.64012195],
           [ 42.        ,   2.9189039 ],
           [ 43.        ,   2.89482297],
           [ 44.        ,   0.59160798],
           [ 45.        ,   1.30766968],
           [ 46.        ,   2.88617394],
           [ 47.        ,   0.50990195],
           [ 48.        ,   2.66270539],
           [ 49.        ,   2.94788059],
           [ 50.        ,   1.02956301],
           [ 51.        ,   0.43588989],
           [ 52.        ,   2.6       ],
           [ 53.        ,   2.90172363],
           [ 54.        ,   3.14006369],
           [ 55.        ,   2.95972972],
           [ 56.        ,   2.9189039 ],
           [ 57.        ,   1.59687194],
           [ 58.        ,   2.93598365],
           [ 59.        ,   0.81240384],
           [ 60.        ,   1.79443584],
           [ 61.        ,   1.19163753],
           [ 62.        ,   2.7513633 ],
           [ 63.        ,   0.71414284],
           [ 64.        ,   0.26457513],
           [ 65.        ,   2.42487113],
           [ 66.        ,   2.24944438],
           [ 67.        ,   3.13687743],
           [ 68.        ,   1.3114877 ],
           [ 69.        ,   2.98998328],
           [ 70.        ,   0.14142136],
           [ 71.        ,   1.90262976],
           [ 72.        ,   1.80554701],
           [ 73.        ,   0.46904158],
           [ 74.        ,   2.64196896],
           [ 75.        ,   1.65227116],
           [ 76.        ,   2.95465734],
           [ 77.        ,   1.12249722],
           [ 78.        ,   3.22645316],
           [ 79.        ,   2.89827535],
           [ 80.        ,   1.90262976],
           [ 81.        ,   0.98994949],
           [ 82.        ,   3.09030743],
           [ 83.        ,   2.74772633],
           [ 84.        ,   3.14324673],
           [ 85.        ,   0.54772256],
           [ 86.        ,   2.65518361],
           [ 87.        ,   1.17898261],
           [ 88.        ,   0.37416574],
           [ 89.        ,   3.46842904],
           [ 90.        ,   2.77308492],
           [ 91.        ,   1.04403065],
           [ 92.        ,   1.17046999],
           [ 93.        ,   1.98997487],
           [ 94.        ,   2.5845696 ],
           [ 95.        ,   1.19582607],
           [ 96.        ,   2.53574447],
           [ 97.        ,   3.17332633],
           [ 98.        ,   2.21133444],
           [ 99.        ,   1.19163753],
           [100.        ,   3.65239647],
           [101.        ,   3.6373067 ],
           [102.        ,   1.09544512],
           [103.        ,   2.74954542],
           [104.        ,   1.64316767],
           [105.        ,   3.12729915],
           [106.        ,   1.        ],
           [107.        ,   3.00998339],
           [108.        ,   1.23693169],
           [109.        ,   1.28062485],
           [110.        ,   2.05669638],
           [111.        ,   3.06594194],
           [112.        ,   0.38729833],
           [113.        ,   3.28633535],
           [114.        ,   0.54772256],
           [115.        ,   0.24494897],
           [116.        ,   1.3190906 ],
           [117.        ,   0.45825757],
           [118.        ,   2.99833287],
           [119.        ,   1.07703296]])




```python
euc(x, X_train[1])
```




    3.5000000000000004




```python
nn = knn._get_k_nearest(dist, 5)
nn
```




    [array([70.        ,  0.14142136]),
     array([115.        ,   0.24494897]),
     array([39.        ,  0.26457513]),
     array([64.        ,  0.26457513]),
     array([36.        ,  0.31622777])]




```python
nnp = knn._get_label_prediction(nn)
nnp
```

    [70, 115, 39, 64, 36]
    [0 5]





    1



Now, we'll create some predictions on our testing data.  In the cell below, use the `.predict()` method to generate predictions for the data stored in `X_test`.


```python
preds = knn.predict(X_test, k=3)
preds
```




    array([[1.],
           [2.],
           [2.],
           [1.],
           [0.],
           [2.],
           [2.],
           [1.],
           [0.],
           [1.],
           [0.],
           [2.],
           [1.],
           [1.],
           [0.],
           [2.],
           [0.],
           [1.],
           [0.],
           [1.],
           [1.],
           [0.],
           [2.],
           [1.],
           [2.],
           [0.],
           [1.],
           [2.],
           [1.],
           [1.]])



And now, for the moment of truth! Let's test the accuracy of our predictions. In the cell below, complete the call to `accuracy_score` by passing in `y_test` and our `preds`!


```python
print("Testing Accuracy: {}".format(accuracy_score(y_test, preds)))
# Expected Output: Testing Accuracy: 0.9736842105263158
```

    Testing Accuracy: 0.9666666666666667


Over 97% accuracy! Not bad for a handwritten machine learning classifier!

## Summary

That was great! In what's next, you'll dive a little deeper into evaluating performance of a KNN algorithm!
