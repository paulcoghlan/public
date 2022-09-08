# ML

## Learning

- Supervised (Labelled) Learning: right answers already given
  - Regression: predict *continuous* value output
    - Linear
    - Polynomial
  - Classification: predict *discrete* value output
    - SVM (Kernels)
    - Logistic (use Sigmoid function)
    - Neural Nets (use Sigmoid function), each layer like Linear Regression
- Unsupervised (Unlabelled) Learning: don't give correct answer
  - Clustering
  - K-Means

## Back propagation

- Cost function
  - Gradient Descent
    - Regularization
    - Feature Normalisation

## Precision vs Recall

- Precision = true positives / (true positives + false positives) <- *Actually returned/how accurate the results are*
- Recall = true positives / (true positives + false negatives) <- *Should have been returned/how wide the net is*

## Probabilities

- Specificity: 
- Sensitivity: 
- Predictive Value: 

## Distributions

### Continuous

- Normal distribution: ("Bell Curve")
  - Symmetric about the mean

### Discrete

- Poisson: used to estimate how likely it is that something will happen "X" number of times
  - All events are independent of each other, the rate of events through time is constant, and events cannot occur simultaneously
  - Mean and the variance will be equal to one another
