# Appendix A. Solutions to the exercises

## Chapter 2: Types of machine learning

For the questions in this chapter, your answers don’t need to match mine. If you have different ideas for models used in these applications, they might be great! I encourage you to look them up in the literature, and if they don’t exist, try to implement them.

Exercise 2.1

For each of the following scenarios, state if it is an example of supervised or unsupervised learning. Explain your answers. In cases of ambiguity, pick one and explain why you picked it.

1. A recommendation system on a social network that recommends potential friends to a user
2. A system in a news site that divides the news into topics
3. The Google autocomplete feature for sentences
4. A recommendation system on an online retailer that recommends to users what to buy based on their past purchasing history
5. A system in a credit card company that captures fraudulent transactions

SOLUTION

Depending on how you interpreted the problem and the dataset, each of these can be considered an example of supervised or unsupervised learning. It is completely OK (and encouraged!) to have different answers, as long as the reasoning behind them is correct.

1. This is an example of both supervised and unsupervised learning. Supervised learning: for a particular user, we can build a classification model where the label of every other user is positive if they are a potential friend and negative if they are not a potential friend.
2. This is also an example of both supervised and unsupervised learning. Supervised learning: a classification model where the label of each news article is the topic, for example, politics, sports, or science. Unsupervised learning: we can cluster the articles and then manually check if the topics in each cluster are similar. If this is the case, then we can manually label each cluster by the most common topic. There are some more advanced unsupervised learning techniques such as latent Dirichlet allocation, which you can learn in this video: [https://www.youtube.com/watch?v=T05t-SqKArY](https://www.youtube.com/watch?v=T05t-SqKArY).
3. This one is more of a supervised learning task. We can build a classification model in which the features are the last few words the user has typed, and the label is the next word they’ll type. In that way, the prediction of the model is the word we’ll suggest to the user.
4. This is similar to a), and it can be considered a supervised or an unsupervised learning problem. Supervised learning: for a particular user, we can build a classification model for all the products, where for each product, we predict whether the user will buy it. We can also build a regression model in which we predict how much money the user will spend on that particular product. Unsupervised learning: we can cluster the users. If a user has bought a product, we can recommend that same product to other users in the cluster. We can also cluster the products, and if a user has bought a product, we recommend products in the same cluster.
5. This one is more of a supervised learning task. We can build a classification model that predicts whether a certain transaction is fraudulent or not, based on the characteristics of that transaction. It can also be seen as an unsupervised learning task in which we cluster the transactions, and those left as outliers have a higher chance of being fraudulent.

Exercise 2.2

For each of the following applications of machine learning, would you use regression or classification to solve it? Explain your answers. In cases of ambiguity, pick one and explain why you picked it.

1. An online store predicting how much money a user will spend on their site
2. A voice assistant decoding voice and turning it into text
3. Selling or buying stock from a particular company
4. YouTube recommending a video to a user

SOLUTION

1. Regression, because we are trying to predict the amount of money that the user spends, and this is a numerical feature.
2. Classification, because we are trying to predict whether the sentence the user has spoken is directed to Alexa, and this is a categorical feature.
3. This could be regression or classification. If we are trying to predict the expected gain or the expected risk to help us in our decision, it is regression. If we are trying to predict whether we should buy the stock, it is classification.
4. This can again be regression or classification. If we are trying to predict how much time the user will spend watching the video in order to recommend it, it is regression. If we are trying to predict whether the user will watch a video, it is classification.

Exercise 2.3

Your task is to build a self-driving car. Give at least three examples of machine learning problems that you would have to solve to build it. In each example, explain whether you are using supervised/unsupervised learning, and if supervised, whether you are using regression or classification. If you are using other types of machine learning, explain which ones and why.

SOLUTION

- A classification model, which, based on the image, determines whether there are pedestrians, stop signs, lanes, other cars, and so on. This is a large area of machine learning called computer vision, which I highly encourage you to explore further!
- A similar classification model as the previous one, which determines what objects are around the car based on the signals from all the different sensors in the car (lidar, etc).
- A machine learning model that finds the closest path to our desired destination. This is not precisely supervised or unsupervised learning. There are some more classical artificial intelligence algorithms such as A* (A-star) search that can be used here.

## Chapter 3: Drawing a line close to our points: Linear regression

Exercise 3.1

A website has trained a linear regression model to predict the amount of minutes that a user will spend on the site. The formula they have obtained is

_t̂_ = 0.8_d_ + 0.5_m_ + 0.5_y_ + 0.2_a_ + 1.5

where _t̂_ is the predicted time in minutes, and _d_, _m_, _y_, and _a_ are indicator variables (namely, they take only the values 0 or 1) defined as follows:

- _d_ is a variable that indicates if the user is on desktop.
- _m_ is a variable that indicates if the user is on mobile device.
- _y_ is a variable that indicates if the user is young (under 21 years old).
- _a_ is a variable that indicates if the user is an adult (21 years old or older).

Example: If a user is 30 years old and on desktop, then _d_ = 1, _m_ = 0, _y_ = 0, and _a_ = 1.

If a 45-year-old user looks at the website from their phone, what is the expected time they will spend on the site?

SOLUTION

In this case, the values of the variables are the following:

- _d_ = 0 because the user is not on desktop.
- _m_ = 1 because the user is on mobile.
- _y_ = 0 because the user is not under 21.
- _a_ = 1 because the user is over 21.

When we plug them into the formula, we get

_t̂_ = 0.8 · 0 + 0.5 · 1 + 0.5 · 0 + 0.2 · 1 + 1.5 = 2.2.

This means the model predicts that this user will spend 2.2 minutes on the website.

Exercise 3.2

Imagine that we trained a linear regression model in a medical dataset. The model predicts the expected lifetime of a patient. To each of the features in our dataset, the model would assign a weight.

a) For the following quantities, state if you believe the weight attached to this quantity is a positive number, a negative number, or zero. Note: if you believe that the weight is a very small number, whether positive or negative, you can say zero.

1. Number of hours of exercise the patient gets per week
2. Number of cigarettes the patient smokes per week
3. Number of family members with heart problems
4. Number of siblings of the patient
5. Whether or not the patient has been hospitalized

b) The model also has a bias. Do you think the bias is positive, negative, or zero?

SOLUTION

a) We’ll make some generalizations based on general medical knowledge. For a particular patient, the following are not necessarily true, but we’ll make the assumption that they are true for the general population:

1. A patient who exercises a lot is expected to live longer than a similar patient who doesn’t. Thus, this weight should be a positive number.
2. A patient who smokes many cigarettes a week is expected to live shorter than a similar patient who doesn’t. Thus, this weight should be a negative number.
3. A patient who has many family members with heart problems has a higher likelihood of having heart problems, and thus they are expected to live shorter than a similar patient that doesn’t have them. Thus, this weight should be a negative number.
4. The number of siblings tends to be independent of the expected lifetime, so we expect this weight to be a very small number, or zero.
5. A patient who has been hospitalized in the past is likely to have had previous health problems. Thus, their expected lifetime is shorter than a similar patient that hasn’t been hospitalized before. Therefore, this weight should be a negative number. Of course, the hospitalization could be for a reason that doesn’t affect expected lifetime (such as a broken leg), but on average, we can say that if a patient has been to the hospital in the past, they have a higher probability to have health problems.

b) The bias is the prediction for a patient for which every feature is zero (i.e., a patient who doesn’t smoke, doesn’t exercise, has zero family members with heart condition, zero siblings, and has never been hospitalized). Because this patient is expected to live a positive number of years, the bias of this model must be a positive number.

Exercise 3.3

The following is a dataset of houses with sizes (in square feet) and prices (in dollars).

|   |   |   |
|---|---|---|
||Size (s)|Prize (p)|
|House 1|100|200|
|House 2|200|475|
|House 3|200|400|
|House 4|250|520|
|House 5|325|735|

Suppose we have trained the model where the prediction for the price of the house based on size is the following:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/p_cf.png) = 2s + 50

1. Calculate the predictions that this model makes on the dataset.
2. Calculate the mean absolute error of this model.
3. Calculate the root mean square error of this model.

SOLUTION

1. The predicted prices based on the model follow:
    - House 1: ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/p_cf.png) = 2 · 100 + 50 = 250
    - House 2: ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/p_cf.png) = 2 · 200 + 50 = 450
    - House 3: ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/p_cf.png) = 2 · 200 + 50 = 450
    - House 4: ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/p_cf.png) = 2 · 250 + 50 = 550
    - House 5: ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/p_cf.png) = 2 · 325 + 50 = 700
2. The mean absolute error is
    
    ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_00_E01.png)
    
3. The mean square error is
    
    ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_00_E02.png)
    

Therefore, the root mean square error is √1550 = 39.37.

Exercise 3.4

Our goal is to move the line with equation _ŷ_ = 2_x_ + 3 closer to the point (_x_, _y_) = (5, 15) using the tricks we’ve learned in this chapter. For the following two problems, use the learning rate _η_ = 0.01.

1. Apply the absolute trick to modify the line above to be closer to the point.
2. Apply the square trick to modify the line above to be closer to the point.

SOLUTION

The prediction that this model makes at the point is _ŷ_ = 2 · 5 + 3 = 13.

1. Because the prediction is 13, which is smaller than the label 15, the point is underneath the line.
    
    In this model, the slope is _m_ = 2 and the _y_-intercept is _b_ = 3. The absolute trick involves adding _x__η_ = 5 · 0.01 = 0.05 to the slope, and _η_ = 0.01 to the _y_-intercept, thus obtaining the model with equation
    
    _ŷ_ = 2.05_x_ + 3.01.
    
2. The square trick involves adding (_y_ – _ŷ_)_x__η_ = (15 – 13) · 5 · 0.01 = 0.1 to the slope, and (_y_ – _ŷ_)_η_ = (15 – 13) · 0.01 = 0.02 to the _y_-intercept, thus obtaining the model with equation
    
    _ŷ_ = 2.1_x_ + 3.02.
    

## Chapter 4: Optimizing the training process: Underfitting, overfitting, testing, and regularization

Exercise 4.1

We have trained four models in the same dataset with different hyperparameters. In the following table we have recorded the training and testing errors for each of the models.

|   |   |   |
|---|---|---|
|Model|Training error|Testing error|
|1|0.1|1.8|
|2|0.4|1.2|
|3|0.6|0.8|
|4|1.9|2.3|

1. Which model would you select for this dataset?
2. Which model looks like it’s underfitting the data?
3. Which model looks like it’s overfitting the data?

SOLUTION

1. The best model is the one with the smallest testing error, which is model 3.
2. Model 4 looks like it is underfitting because it has large training and testing errors.
3. Models 1 and 2 look like they are overfitting, because they have small training errors but large testing errors.

Exercise 4.2

We are given the following dataset:

|   |   |
|---|---|
|_x_|_y_|
|1|2|
|2|2.5|
|3|6|
|4|14.5|
|5|34|

We train the polynomial regression model that predicts the value of _y_ as _ŷ_, where

_ŷ_ = 2_x_2 _–_ 5_x_ + 4_._

If the regularization parameter is λ = 0.1 and the error function we’ve used to train this dataset is the mean absolute value (MAE), determine the following:

1. The lasso regression error of our model (using the L1-norm)
2. The ridge regression error of our model (using the L2-norm)

SOLUTION

First we need to find the predictions to calculate the mean absolute error of the model. In the following table, we can find the prediction calculated by the formula _ŷ_ = 2_x_2 _–_ 5_x_ + 4, and the absolute value of the difference between the prediction and the label |_y_ – _ŷ_|.

|   |   |   |   |
|---|---|---|---|
|_x_|_y_|_ŷ_|\|_y – ŷ_\||
|1|2|1|1|
|2|2.5|2|0.5|
|3|6|7|1|
|4|14.5|16|1.5|
|5|34|29|5|

Thus, the mean absolute error is the average of the numbers in the fourth row, namely

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_00_E03.png)

1. First we need to find the L1-norm of the polynomial. This is the sum of the absolute values of the nonconstant coefficients, namely, |2| + |–5| = 7. To find the L1-regularization cost of the model, we add the mean absolute error and the L1-norm times the regularization parameter, to obtain 1.8 + 0.1 · 7 = 2.5.
2. In a similar way, we find the L1-norm of the polynomial by adding the squares of nonconstant coefficients to get 22 + (–5)2 = 29. As before, the L2-regularization cost of the model is 1.8 + 0.1 · 29 = 4.7.

## Chapter 5: Using lines to split our points: The perceptron algorithm

Exercise 5.1

The following is a dataset of patients who have tested positive or negative for COVID-19. Their symptoms are cough (C), fever (F), difficulty breathing (B), and tiredness (T).

|   |   |   |   |   |   |
|---|---|---|---|---|---|
||Cough (C)|Fever (F)|Difficulty breathing (B)|Tiredness (T)|Diagnosis (D)|
|Patient 1||X|X|X|Sick|
|Patient 2|X|X||X|Sick|
|Patient 3|X||X|X|Sick|
|Patient 4|X|X|X||Sick|
|Patient 5|X|||X|Healthy|
|Patient 6||X|X||Healthy|
|Patient 7||X|||Healthy|
|Patient 8||||X|Healthy|

Build a perceptron model that classifies this dataset.

hint You can use the perceptron algorithm, but you may be able to eyeball a good perceptron model that works.

SOLUTION

If we count how many symptoms each patient has, we notice that the sick patients show three or more symptoms, whereas the healthy patients show two or fewer symptoms. Thus, the following model works to predict the diagnosis _D_:

_D̂_ = _step_(_C_ + _F_ + _B_ + _T_ – 2.5)

Exercise 5.2

Consider the perceptron model that assigns to the point (_x_1, _x_2) the prediction _ŷ_ = _step_(2_x_1 + 3_x_2 – 4). This model has as a boundary line with equation 2_x_1 + 3_x_2 – 4 = 0. We have the point _p_ = (1, 1) with label 0.

1. Verify that the point _p_ is misclassified by the model.
2. Calculate the perceptron error that the model produces at the point _p_.
3. Use the perceptron trick to obtain a new model that still misclassifies _p_ but that produces a smaller error. You can use _η_ = 0.01 as the learning rate.
4. Find the prediction given by the new model at the point _p_, and verify that the perceptron error obtained is smaller than the original.

SOLUTION

1. The prediction for the point _p_ is
    
    _ŷ_ = _step_(2_x_1 + 3_x_2 – 4) = _step_(2 · 1 + 3 · 1 – 4) = _step_(1) = 1.
    
    Because the label of the point is 0, the point is misclassified.
    
2. The perceptron error is the absolute value of the score. The score is 2_x_1 + 3_x_2 – 4 = 2 · 1 + 3 · 1 – 4 = 1, so the perceptron error is 1.
3. The weights of the model are 2, 3, and –4, and the coordinates of the point are (1, 1). The perceptron trick does the following:
    
    - Replaces 2 with 2 – 0.01 · 1 = 1.99
    - Replaces 3 with 3 – 0.01 · 1 = 2.99
    - Replaces –4 with –1 – 0.01 · 1 = –4.01
    
    Thus, the new model is the one that makes the prediction _ŷ_ = _step_(1.99_x_1 + 2.99_x_2 – 4.01).
    
4. Note that at our point, the new prediction is _ŷ_ = _step_(1.99_x_1 + 2.99_x_2 – 4.01) = _step_(0.97) = 0, which means the model still misclassifies the point. However, the new perceptron error is |1.99 · 1 + 2.99 · 1 – 4.01| = 0.97, which is smaller than 1, the previous error.

Exercise 5.3

Perceptrons are particularly useful for building logical gates such as AND and OR.

1. Build a perceptron that models the AND gate. In other words, build a perceptron to fit the following dataset (where _x_1, _x_2 are the features and _y_ is the label):
    
    |   |   |   |
    |---|---|---|
    |_x_1|_x_2|_y_|
    |0|0|0|
    |0|1|0|
    |1|0|0|
    |1|1|1|
    
2. Similarly, build a perceptron that models the OR gate, given by the following dataset:
    
    |   |   |   |
    |---|---|---|
    |_x_1|_x_2|_y_|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|1|
    
3. Show that there is no perceptron that models the XOR gate, given by the following dataset:
    
    |   |   |   |
    |---|---|---|
    |_x_1|_x_2|_y_|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|0|
    

SOLUTION

For simplicity, we plot the data points in the figure that follows.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-11.png)

Note that a perceptron classifier is precisely a line that would split the black and white dots in the above plots.

For the AND and OR datasets, we can easily split the black and white points with a line, as seen next.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-21.png)

1. Many equations work for the line separating the AND dataset. We’ll pick the line with equation _x_1 + _x_2 – 1.5. Thus, the perceptron that classifies this dataset makes the prediction _ŷ_ = _step_(_x_1 + _x_2 – 1.5).
2. Similarly, many equations work for the OR dataset, and we pick the line with equation _x_1 + _x_2 – 0.5. The equation for the prediction is _ŷ_ = _step_(_x_1 + _x_2 – 0.5).
3. Notice that the dataset for XOR is impossible to separate using a single line. Thus, there is no perceptron model that perfectly fits the XOR dataset. However, a combination of perceptrons can separate this dataset. These are also called multilayer perceptrons, or neural networks, and we’ll see them in chapter 10. If you’re curious, take a look at exercise 10.2.

## Chapter 6: A continuous approach to splitting points: Logistic classifiers

Exercise 6.1

A dentist has trained a logistic classifier on a dataset of patients to predict if they have a decayed tooth. The model has determined that the probability that a patient has a decayed tooth is

_σ_(_d_ + 0.5_c_ – 0.8),

where

- _d_ is a variable that indicates whether the patient has had another decayed tooth in the past, and
- _c_ is a variable that indicates whether the patient eats candy.

For example, if a patient eats candy, then _c_ = 1, and if they don’t, then _c_ = 0. What is the probability that a patient that eats candy and was treated for a decayed tooth last year has a decayed tooth today?

SOLUTION

If the patient eats candy, then _c_ = 1. If the patient was treated for tooth decay last year, then _d_ = 1. Thus, according to the model, the probability that the patient has a decayed tooth is

_σ_(1 + 0.5 · 1 – 0.8) = _σ_(0.7) = 0.668.

Exercise 6.2

Consider the logistic classifier that assigns to the point (_x_1, _x_2) the prediction _ŷ_ = _σ_(2_x_1 + 3_x_2 – 4), and the point _p_ = (1, 1) with label 0.

1. Calculate the prediction _ŷ_ that the model gives to the point _p_.
2. Calculate the log loss that the model produces at the point _p_.
3. Use the logistic trick to obtain a new model that produces a smaller log loss. You can use _η_ = 0.1 as the learning rate.
4. Find the prediction given by the new model at the point _p_, and verify that the log loss obtained is smaller than the original.

SOLUTION

1. The prediction is _ŷ_ = _σ_(2 · 1 + 3 · 1 – 4) = _σ_(1) = 0.731
2. The log loss is
    
    _log loss_ = –_y_ _ln_ (_ŷ_) – (1 – _y_) _ln_ (1 – _ŷ_)
    
                 = –0 _ln_ (0.731) – (1 – 0) _ln_ (1 – 0.731)
    
                 = 1.313.
    
3. Recall that the perceptron trick for the logistic regression model with prediction _ŷ_ = _σ_(_w_1_x_1 + _w_1_x_1 + _b_) gives us the following new weights:
    
    - _w_1_'_ = _w_1 + _η_(_y_ – _ŷ_) _x_1 for _i_ = 1,2
    - _b__'_ = _b_ + _η_(_y_ – _ŷ_) for _i_ = 1,2
    
    These are the values to plug into the previous formulas:
    
    - _y_ = 0
    - _ŷ_ = 0.731
    - _w_1 = 2
    - _w_2 = 3
    - _b_ = –4
    - _η_ = 0.1
    - _x_1 = 1
    - _x_2 = 1
    
    We obtain the following new weights for our classifier:
    
    - _w_1' = 2 + 0.1 · (0 – 0.731) · 1 = 1.9269
    - _w_2' = 3 + 0.1 · (0 – 0.731) · 1 = 2.9269
    - _b_ = –4 + 0.1 · (0 – 0.731) = –4.0731
    
    Thus, our new classifier is the one that makes the prediction _ŷ_ = _σ_(1.9269_x_1 + 2.9269_x_2 – 4.0731).
    
    The prediction at the point _p_ is _ŷ_ =_σ_(1.9269 · 1 + 2.9269 · 1 – 4.0731) = 0.686. Notice that because the label is 0, the prediction has improved from the original 0.731, to the actual 0.686.
    
4. The log loss for this prediction is –_y_ _ln_(_ŷ_) – (1 – _y_) _ln_(1 – _ŷ_) = –0 _ln_(0.686) – (1 – 0) _ln_(1 – 0.686) = 1.158. Note that this is smaller than the original log loss of 1.313.

Exercise 6.3

Using the first model in exercise 6.2, construct a point for which the prediction is 0.8.

hint First find the score that will give a prediction of 0.8, and recall that the prediction is _ŷ_ = _σ_(score).

SOLUTION

First, we need to find a score such that _σ_(score) = 0.8. This is equivalent to

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_00_E04.png)

Recall that for the point (_x_1, _x_2), the score is 2_x_1 + 3_x_2 – 4. Many points (_x_1, _x_2) satisfy that the score is 1.386, but in particular, let’s pick one in which _x_2 = 0 for convenience. We need to solve the equation 2_x_1 + 3 · 0 – 4 = 1.386, which has as a solution, _x_1 = 2.693. Thus, a point that gives a prediction of 0.8 is the point (2.693, 0).

## Chapter 7: How do you measure classification models? Accuracy and its friends

Exercise 7.1

A video site has established that a particular user likes animal videos and absolutely nothing else. In the next figure, we can see the recommendations that this user got when logging in to the site.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-31.png)

If this is all the data we have on the model, answer the following questions:

1. What is the accuracy of the model?
2. What is the recall of the model?
3. What is the precision of the model?
4. What is the _F_1-score of the model?
5. Would you say that this is a good recommendation model?

SOLUTION

First, let’s write the confusion matrix. In this case, we label the videos that are about animals as _positive_, and the videos that are recommended as _predicted positive_.

- There are four recommended videos. Out of them, three are about animals, which means they are good recommendations. The other one is not about animals, so it is a false positive.
- There are six videos that are not recommended. Out of them, two are about animals, which should have been recommended. Thus, they are false negatives. The other four are not about animals, so it was correct not to recommend them.

Thus, the confusion matrix is the following:

|   |   |   |
|---|---|---|
||Predicted positive (recommended)|Predicted negative (not recommended)|
|Positive (about animals)|3|2|
|Negative (not about animals)|1|4|

Now we can calculate the metrics.

1. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_03_E01.png)
2. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_03_E02.png)
3. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_03_E03.png)
4. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_03_E04.png)
5. This is a subjective answer. A medical model with these metrics may not be good enough. However, if a recommendation model has decent accuracy, precision, and recall, it is considered a good model, because making a couple of mistakes in a recommendation model is not as crucial.

Exercise 7.2

Find the sensitivity and specificity of the medical model with the following confusion matrix:

|   |   |   |
|---|---|---|
||Predicted sick|Predicted healthy|
|Sick|120|22|
|Healthy|63|795|

SOLUTION

The sensitivity is the number of correctly predicted sick people divided by the total number of sick people. This is 120/142= 0.845.

The specificity is the number of correctly predicted healthy people divided by the total number of healthy people. This is 795/858= 0.927.

Exercise 7.3

For the following models, determine which error is worse, a false positive or a false negative. Based on that, determine which of the two metrics, precision or recall, we should emphasize when evaluating each of the models.

1. A movie recommendation system that predicts whether a user will watch a movie.
2. An image-detection model used in self-driving cars that detects whether an image contains a pedestrian.
3. A voice assistant at home that predicts whether the user gave it an order.

SOLUTION

note In all of the following models, a false negative and a false positive are bad, and we want to avoid both of them. However, we show an argument for which one of the two is worse. These are all conceptual questions, so if you have a different idea, as long as you can argue it well, it is valid! These are the kind of discussions that arise in a team of data scientists, and it is important to have healthy opinions and arguments supporting each point of view.

1. In this model, we label the movies that the user wants to watch as positives. A false positive occurs any time we recommend a movie that the user doesn’t want to watch. A false negative occurs any time there is a movie that the user wants to watch, but we don’t recommend it. Which is worse, a false negative or a false positive? Because the homepage shows many recommendations and the user ignores most of them, this model has many false negatives that don’t affect the user experience much. However, if there is a great movie that the user would like to watch, it is crucial to recommend it to them. Therefore, in this model, a false negative is worse than a false positive, so we should evaluate this model using **recall**.
2. In this model, we label the existence of a pedestrian as a positive. A false positive occurs when there is no pedestrian, but the car thinks there is a pedestrian. A false negative occurs when the car doesn’t detect a pedestrian that is in front of the car. In the case of a false negative, the car may hit a pedestrian. In the case of a false positive, the car may brake unnecessarily, which may or may not lead to an accident. Although both are serious, it is much worse to hit a pedestrian. Therefore, in this model, a false negative is worse than a false positive, so we should evaluate this model using **recall**.
3. In this model, we label a voice command as a positive. A false positive occurs when the user is not talking to the voice assistant, but the voice assistant responds. A false negative occurs when the user is talking to the voice assistant, but the voice assistant doesn’t respond. As a personal choice, I prefer to have to repeat to my voice assistant than to have her speak to me out of the blue. Thus, in this model, a false positive is worse than a false negative, so we should evaluate this model using **precision**.

Exercise 7.4

We are given the following models:

1. A self-driving car model for detecting a pedestrian based on the image from the car’s camera
2. A medical model for diagnosing a deadly illness based on the patient’s symptoms
3. A recommendation system for movies based on the user’s previous movies watched
4. A voice assistant that determines whether the user needs assistance given the voice command
5. A spam-detection model that determines whether an email is spam based on the words in the email

We are given the task of evaluating these models using _F_β-scores. However, we haven’t been given the values of _β_ to use. What value of _β_ would you use to evaluate each of the models?

SOLUTION

Remember that for models in which precision is more important than recall, we use an _F_β-score with a small value of _β_. In contrast, for models in which recall is more important than precision, we use an _F_β-score with a large value of _β_.

note If you have different scores than this solution, that is completely OK, as long as you have an argument for which is more important between precision and recall, and for the value of _β_ that you choose.

- For the self-driving car and the medical models, recall is tremendously important because we want very few false negatives. Thus, I would use a large value of _β_, such as 4.
- For the spam-detection model, precision is important, because we want very few false positives. Thus, I would use a small value of _β_, such as 0.25.
- For the recommendation system, recall is more important (see exercise 7.3), although precision also matters. Thus, I would use a large value of _β_, such as 2.
- For the voice assistant, precision is more important, although recall also matters (see exercise 7.3). Thus, I would use a small value for _β_, such as 0.5.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-41.png)

## Chapter 8: Using probability to its maximum: The naive Bayes model

Exercise 8.1

For each pair of events A and B, determine whether they are independent or dependent. For (a) to (d), provide mathematical justification. For (e) and (f) provide verbal justification.

Throwing three fair coins:

1. A: First one falls on heads. B: Third one falls on tails.
2. A: First one falls on heads. B: There is an odd number of heads among the three throws.
    
    Rolling two dice:
    
3. A: First one shows a 1. B: Second one shows a 2.
4. A: First one shows a 3. B: Second one shows a higher value than the first one.
    
    For the following, provide a verbal justification. Assume that for this problem, we live in a place with seasons.
    
5. A: It’s raining outside. B: It’s Monday.
6. A: It’s raining outside. B: It’s June.

SOLUTION

Some of the following can be deduced by intuition. However, sometimes intuition fails when determining whether two events are independent. For this reason, unless the events are obviously independent, we’ll stick to checking whether two events A and B are independent if _P_(_A_ ∩ _B_) = _P_(_A_) _P_(_B_).

1. Because A and B correspond to tossing different coins, they are independent events.
2. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea01.png) because flipping a fair coin results in two equally likely scenarios. For the calculation of _P_(_B_), we’ll use “h” for heads and “t” for tails. This way, the event “hth” corresponds to the first and third coin toss landing on heads and the second one landing on tails. Thus, if we throw three coins, the eight equally likely possibilities are {hhh, hht, hth, htt, thh, tht, tth, ttt}. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea02.png) because among the eight equally likely possibilities (hhh, hht, hth, htt, thh, tht, tth, ttt), only four of them have an odd number of heads, namely, {hhh, htt, tht, tth}. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea03.png) because among the eight possibilities, only two satisfy that the first one falls on heads, and there are an odd number of heads, namely, {hhh, htt}. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea04.png), so the events A and B are independent.
3. Because A and B correspond to tossing different dice, they are independent events.
4. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea05.png), because it corresponds to tossing a die and obtaining a particular value. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea06.png) for the following reason. Notice that the 36 equally likely possibilities for the scores of the two dice are {11, 12, 13, …, 56, 66}. In six of these, the two dice show the same value. The remaining 30 correspond to 15 in which the first value is higher, and 15 in which the second value is higher, by symmetry. Therefore, there are 15 scenarios in which the second die shows a higher value than the third one, so ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea07.png). ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea08.png) for the following reason. If the first die falls on 3, we have a total of six equally likely scenarios, namely, {31, 32, 33, 34, 35, 36}. Out of these six, the second number is higher for three of them. Thus, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ea09.png). Because _P_(_A_) _P_(_B_) ≠ _P_(_A_ ∩ _B_), the events A and B are dependent.
5. For this problem, we’ll make the assumption that A and B are independent, namely, that weather is not dependent on the day of the week. This is a fair assumption, given our knowledge of weather, but if we wanted to be surer, we could look at weather datasets and verify this by calculating the corresponding probabilities.
6. Because we’ve assumed that we live in a place with seasons, June is summer in the northern hemisphere and winter in the southern hemisphere. Depending on where we live, it may rain more in the winter or in the summer. Thus, we can assume that events A and B are dependent.

Exercise 8.2

There is an office where we have to go regularly for some paperwork. This office has two clerks, Aisha and Beto. We know that Aisha works there three days a week, and Beto works the other two However, the schedules change every week, so we never know which three days Aisha is there, and which two days Beto is there.

1. If we show up on a random day to the office, what is the probability that Aisha is the clerk?
    
    We look from outside and notice that the clerk is wearing a red sweater, although we can’t tell who the clerk is. We’ve been going to that office a lot, so we know that Beto tends to wear red more often than Aisha. In fact, Aisha wears red one day out of three (one-third of the time), and Beto wears red one day out of two (half of the time).
    
2. What is the probability that Aisha is the clerk, knowing that the clerk is wearing red today?

SOLUTION

Let’s use the following notation for events:

- A: the event that the clerk is Aisha
- B: The event that the clerk is Beto
- R: The event that the clerk is wearing red

1. Because Aisha works at the office three days and Beto works two days, the probability that Aisha is the clerk is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eb01.png), or 60%. In addition, the probability that Beto is the clerk is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eb02.png), or 40%.
2. Intuitively, because Beto wears red more often than Aisha, we imagine that the probability that the clerk is Aisha is lower than in part a). Let’s check whether the math agrees with us. We know the clerk is wearing red, so we need to find the probability that the clerk is Aisha _knowing that_ the clerk is wearing red. This is _P_(_A_|_R_).

The probability that Aisha wears red is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eb03a.png), so ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eb03b.png). The probability that Beto wears red is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eb04a.png), so ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eb04b.png).

We can use Bayes theorem to obtain

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_03_E05.png)

A similar calculation shows that the probability that Beto is the clerk is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ec01.png), or 50%.

In effect, the probability that Aisha is the clerk is smaller than the one obtained in part a), so our intuition was right.

Exercise 8.3

The following is a dataset of patients who have tested positive or negative for COVID-19. Their symptoms are cough (C), fever (F), difficulty breathing (B), and tiredness (T).

|   |   |   |   |   |   |
|---|---|---|---|---|---|
||Cough (C)|Fever (F)|Difficulty breathing (B)|Tiredness (T)|Diagnosis|
|Patient 1||X|X|X|Sick|
|Patient 2|X|X||X|Sick|
|Patient 3|X||X|X|Sick|
|Patient 4|X|X|X||Sick|
|Patient 5|X|||X|Healthy|
|Patient 6||X|X||Healthy|
|Patient 7||X|||Healthy|
|Patient 8||||X|Healthy|

The goal of this exercise is to build a naive Bayes model that predicts the diagnosis from the symptoms. Use the naive Bayes algorithm to find the following probabilities:

note For the following questions, the symptoms that are not mentioned are completely unknown to us. For example, if we know that the patient has a cough, but nothing is said about their fever, it does not mean the patient doesn’t have a fever.

1. The probability that a patient is sick given that the patient has a cough
2. The probability that a patient is sick given that the patient is not tired
3. The probability that a patient is sick given that the patient has a cough and a fever
4. The probability that a patient is sick given that the patient has a cough and a fever, but no difficulty breathing

SOLUTION

For this problem, we have the following events:

- C: the event that the patient has a cough
- F: the event that the patient has a fever
- B: the event that the patient has difficulty breathing
- T: the event that the patient is tired
- S: the event that the patient has been diagnosed as sick
- H: the event that the patient has been diagnosed as healthy

Furthermore, _A_c denotes the complement (opposite) of the event _A_. Thus, for example, _T_c represents the event that the patient is not tired.

First, let’s calculate _P_(_S_) and _P_(_H_). Note that because the dataset contains four healthy and four sick patients, both of these (prior) probabilities are ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/frac_1-2.png), or 50%.

  

1. Because four patients have a cough and three of them are sick, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ed01.png), or 75%.
    
    Equivalently, we can use Bayes’ theorem in the following way: first, we calculate ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ed02.png) by noticing that there are four sick patients, and three of them have a cough. We also notice that ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ed03.png), because there are four healthy patients, and only one of them has a cough.
    
    Now we can use the formula
    
    ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_E01.png)
    
2. Because four patients have a cough and three of them are sick, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ee01.png), or 33.3%.
    
    We can also use Bayes’ theorem as before. Notice that ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ee02.png) because only one out of the four sick patients is not tired. Also, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ee03.png), because two out of the four healthy patients are not tired.
    
    By Bayes’ theorem,
    
    ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_E02.png)
    
3. _C_ ∩ _F_ represents the event that the patient has a cough and a fever, so we need to calculate _P_(_S_|_C_ ∩ _F_).
    
    Recall from part a) that ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ef01.png) and ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ef02.png).
    
    Now we need to calculate _P_(_F_|_S_) and _P_(_F_|_H_). Note that because there are four sick patients and three of them have a fever, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ef03.png). Similarly, two out of the four healthy patients have a fever, so ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Ef04.png).
    
    We are ready to use the naive Bayes algorithm to estimate the probability that the patient is sick given that they have a cough and fever. Using the formula in the section “What about two words? The naive Bayes algorithm” in chapter 8, we get
    
    ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_E03.png)
    
4. For this exercise we need to find _P_(_S_|_C_ ∩ _F_ ∩ _B_c)
    
    Note that because there are four sick patients and only one of them has no difficulty breathing, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eg01.png). Similarly, there are four healthy patients and three of them have no difficulty breathing, so ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_Eg02.png).
    
    As before, we can use the naive Bayes algorithm.
    

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_08_E04.png)

## Chapter 9: Splitting data by asking questions: Decision trees

Exercise 9.1

In the following spam-detection decision tree model, determine whether an email from your mom with the subject line “Please go to the store, there’s a sale,” will be classified as spam.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-51.png)

SOLUTION

First we check whether the sender is unknown. Because the sender is our mom, the sender is not unknown. Thus, we take the branch on the right. We must check whether the email contains the word “sale.” The email contains the word “sale,” so the classifier (incorrectly) classifies it as spam.

Exercise 9.2

Our goal is to build a decision tree model to determine whether credit card transactions are fraudulent. We use the dataset of credit card transactions below, with the following features:

- **Value**: value of the transaction.
- **Approved vendor**: the credit card company has a list of approved vendors. This variable indicates whether the vendor is in this list.

|   |   |   |   |
|---|---|---|---|
||Value|Approved vendor|Fraudulent|
|Transaction 1|$100|Not approved|Yes|
|Transaction 2|$100|Approved|No|
|Transaction 3|$10,000|Approved|No|
|Transaction 4|$10,000|Not approved|Yes|
|Transaction 5|$5,000|Approved|Yes|
|Transaction 6|$100|Approved|No|

Build the first node of the decision tree under the following specifications:

1. Using the Gini impurity index
2. Using entropy

SOLUTION

In both cases, the best split is obtained using the Approved vendor feature, as in the next image.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-70.png)

Let’s call the transactions _T_1, _T_2, _T_3, _T_4, _T_5, and _T_6.

First, let’s look at all the following splits we can make. The split using Approved vendor is easy, because this is a categorical variable with two categories. The Value column is more complicated—we can use it to split the data in two possible ways. One is when the cutoff is some value between $100 and $5,000, and the other one when it is some value between $5,000 and $10,000. To summarize, these are all the possible splits:

- **Value 1**: where the cutoff value is between $100 and $5,000. The two classes here are {_T_1, _T_2, _T_6} and {_T_3, _T_4, _T_5}.
- **Value 2**: where the cutoff value is some value between $5,000 and $10,000. The two classes here are {_T_1, _T_2, _T_5, _T_6} and {_T_3, _T_4}.
- **Approved vendor**: the two classes are “approved” and “not approved,” or equivalently, {_T_2, _T_3, _T_5, _T_6} and {_T_1, _T_4}.

1. Let’s calculate the Gini impurity index for each one of the following four splits:
    
    **Value 1**: cutoff value between $100 and $5,000
    
    Note that for the first class {_T_1, _T_2, _T_6}, the labels in the Fraudulent column are {“yes”, “no”, “no”}. The Gini impurity index of this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E01.png).
    
    Note that for the second class {_T_3, _T_4, _T_5}, the labels in the Fraudulent column are {“no”, “yes”, “yes”}. The Gini impurity index of this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E02.png).
    
    Thus, the weighted Gini impurity index for this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E03.png).
    
    **Value 2**: cutoff value between $5,000 and $10,000
    
    For the first class {_T_1, _T_2, _T_5, _T_6}, the labels in the Fraudulent column are {“yes”, “no”, “yes”, “no”}. The Gini impurity index of this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E04.png).
    
    Note that for the second class {_T_3, _T_4}, the labels in the Fraudulent column are {“no”, “yes”}. The Gini impurity index of this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E05.png).
    
    Thus, the weighted Gini impurity index for this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E06.png)_._
    
    Approved vendor:
    
    For the first class {_T_2, _T_3, _T_5, _T_6}, the labels in the Fraudulent column are {“no”, “no”, “yes,” “no”}. The Gini impurity index of this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E07.png).
    
    For the second class {_T_1, _T_4}, the labels in the Fraudulent column are {“yes”, “yes”}. The Gini impurity index of this split is 1 – 12 = 0.
    
    Thus, the weighted Gini impurity index for this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E08.png).
    
    Notice that out of these three values, the lowest is 0.25, corresponding to the Approved vendor column. This implies that the best way to split this data is using the Approved vendor feature.
    
2. For this part, we’ve done most of the heavy lifting already. We’ll follow the same procedure as in part a), except calculating the entropy at each stage instead of the Gini impurity index.
    
    **Value 1**: cutoff value between $100 and $5,000
    
    The entropy of the set {“yes”, “no”, “no”} is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E09.png).
    
    The entropy of the set {“no”, “yes”, “yes”} is also ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E10.png).
    
    Thus, the weighted entropy for this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E11.png).
    
    **Value 2**: cutoff value between $5,000 and $10,000
    
    The entropy of the set {“yes”, “no”, “yes”, “no”} is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E12.png).
    
    The entropy of the set {“no”, “yes”} is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E13.png).
    
    Thus, the weighted entropy for this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E14.png).
    
    Approved vendor:
    
    The entropy of the set {“no”, “no”, “yes”, “no”} is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E15.png).
    
    The entropy of the set {“yes”, “yes”} is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E16.png).
    
    Thus, the weighted entropy for this split is ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_09_E17.png).
    
    Notice that among these three, the smallest entropy is 0.541, corresponding to the Approved vendor column. Thus, the best way to split this data is, again, using the Approved vendor feature.
    

Exercise 9.3

A dataset of patients who have tested positive or negative for COVID-19 follows. Their symptoms are cough (C), fever (F), difficulty breathing (B), and tiredness (T).

|   |   |   |   |   |   |
|---|---|---|---|---|---|
||Cough (C)|Fever (F)|Difficulty breathing (B)|Tiredness (T)|Diagnosis|
|Patient 1||X|X|X|Sick|
|Patient 2|X|X||X|Sick|
|Patient 3|X||X|X|Sick|
|Patient 4|X|X|X||Sick|
|Patient 5|X|||X|Healthy|
|Patient 6||X|X||Healthy|
|Patient 7||X|||Healthy|
|Patient 8||||X|Healthy|

Using accuracy, build a decision tree of height 1 (a decision stump) that classifies this data. What is the accuracy of this classifier on the dataset?

SOLUTION

Let’s call the patients _P_1 up to _P_8. The sick patients will be denoted by “s,” and the healthy ones by “h.”

First notice that the first split can be any of the four features C, F, B, and T. Let’s first calculate the accuracy of the classifier obtained by splitting the data on feature C, namely, the classifier we build based on the question, “Does the patient have a cough?”

Splitting based on the C feature:

- Patients with a cough: {_P_2, _P_3, _P_4, _P_5}. Their labels are {_s_, _s_, _s_, _h_}.
- Patients without a cough: {_P_1, _P_6, _P_7, _P_8} . Their labels are {_s_, _h_, _h_, _h_}.

Looking at this, we can see that the most accurate classifier (only based on the C feature) is the one that classifies every person with a cough as sick and every person without a cough as healthy. This classifier correctly classifies six out of the eight patients (three sick and three healthy), so its accuracy is 6/8, or 75%.

Now, let’s follow the same procedure with the other three features.

Splitting based on the F feature:

- Patients with a fever: {_P_1, _P_2, _P_4, _P_6, _P_7}. Their labels are {_s_, _s_, _s_, _h_, _h_}.
- Patients without a fever: {_P_3, _P_5, _P_8}. Their labels are {_s_, _h_, _h_}.

Looking at this, we can see that the most accurate classifier (only based on the F feature) is the one that classifies every patient with a fever as sick and every patient without a fever as healthy. This classifier correctly classifies five out of the eight patients (three sick and two healthy), so its accuracy is 5/8, or 62.5%.

Splitting based on the B feature:

- Patients showing difficulty breathing: {_P_1, _P_3, _P_4, _P_5}. Their labels are {_s_, _s_, _s_, _h_}.
- Patients not showing difficulty breathing: {_P_2, _P_6, _P_7, _P_8}. Their labels are {_s_, _h_, _h_, _h_}.

Looking at this, we can see that the most accurate classifier (based only on the B feature) is the one that classifies every patient showing difficulty breathing as sick and every patient not showing difficulty breathing as healthy. This classifier correctly classifies six out of the eight patients (three sick and three healthy), so its accuracy is 6/8, or 75%.

Splitting based on the T feature:

- Patients that are tired: {_P_1, _P_2, _P_3, _P_5, _P_8}. Their labels are {_s_, _s_, _s_, _h_, _h_}.
- Patients that are not tired: {_P_4, _P_5, _P_7}. Their labels are {_s_, _h_, _h_}.

Looking at this, we can see that the most accurate classifier (based only on the F feature) is the one that classifies every tired patient as sick and every patient that is not tired as healthy. This classifier correctly classifies five out of the eight patients (three sick and two healthy), so its accuracy is 5/8, or 62.5%.

Note that the two features that give us the best accuracy are C (cough) and B (difficulty breathing). The decision tree will pick one of these at random. Let’s pick the first one, C. After we split the data using the C feature, we obtain the following two datasets:

- Patients with a cough: {_P_2, _P_3, _P_4, _P_5}. Their labels are {_s_, _s_, _s_, _h_}.
- Patients without a cough: {_P_1, _P_6, _P_7, _P_8}. Their labels are {_s_, _h_, _h_, _h_}.

This gives us our tree of depth 1 that classifies the data with a 75% accuracy. The tree is depicted in the next figure.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-71.png)

## Chapter 10: Combining building blocks to gain more power: Neural networks

Exercise 10.1

The following image shows a neural network in which all the activations are sigmoid functions.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-81.png)

What would this neural network predict for the input (1,1)?

SOLUTION

Let’s call the outputs of the middle nodes _η_1 and _η_2. These are calculated as follows:

_h_1 = _σ_(1 · _x_1 – 2 · _x_2 – 1)

_h_2 =_σ_(–1 · _x_1 + 3 · _x_2 – 1)

Plugging in _x_1 = 1 and _x_2 = 1, we get the following:

_h_1 = _σ_(–2) = 0.119

_h_2 = _σ_(1) = 0.731

The final layer is

_ŷ_ = _σ_(–1 · _h_1 + 2 · _h_2 + 1).

Replacing the values previously obtained for _h_1 and _h_2, we get

_ŷ_ = _σ_(–0.119 + 2 · 0.731 + 1) = _σ_(2.343) = 0.912.

Thus, the output of the neural network is 0.912.

Exercise 10.2

As we learned in exercise 5.3, it is impossible to build a perceptron that mimics the XOR gate. In other words, it is impossible to fit the following dataset with a perceptron and obtain 100% accuracy:

|   |   |   |
|---|---|---|
|_x_1|_x_2|_y_|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

This is because the dataset is not linearly separable. Using a neural network of depth 2, build a perceptron that mimics the XOR gate shown previously. As the activation functions, use the step function instead of the sigmoid function to get discrete outputs.

hint This will be hard to do using a training method; instead, try eyeballing the weights. Try (or search online how) to build an XOR gate using AND, OR, and NOT gates, and use the results of exercise 5.3 to help you.

SOLUTION

Note that the following combination of AND, OR, and NOT gates forms an XOR gate (where the NAND gate is the combination of an AND gate and a NOT gate).

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-91.png)

The following truth table illustrates it.

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|_x_1|_x_2|_h_1 = _x_1 _OR_ _x_2|_h_2 = _x_1 _NAND_ _x_2|_h_1 _AND_ _η_2|_x_1 _XOR_ _x_2|
|0|0|0|1|0|0|
|0|1|1|1|1|1|
|1|0|1|1|1|1|
|1|1|1|0|0|0|

As we did in exercise 5.3, here are perceptrons that mimic the OR, NAND, and AND gates. The NAND gate is obtained by negating all the weights in the AND gate.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-101.png)

Joining these together, we get the neural network shown in the next figure.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-112.png)

I encourage you to verify that this network does indeed mimic the XOR logic gate. This is done by inputting the four vectors (0,0), (0,1), (1,0), (1,1) through the network and verifying that the outputs are 0, 1, 1, 0.

Exercise 10.3

At the end of the section “A graphical representation of neural networks,” we saw that the neural network in figure 10.13 with the activation function doesn’t fit the dataset in table 10.1, because the point (1,1) is misclassified.

1. Verify that this is the case.
2. Change the weights so that the neural network classifies every point correctly.

SOLUTION

1. For the point (_x_a, _x_b) = (1, 1), the predictions are the following:
    
    _C_ = _σ_(6 · 1 + 10 · 1 – 15) = _σ_(1) = 0.731
    
    _F_ = _σ_(10 · 1 + 6 · 1 – 15) = _σ_(1) = 0.731
    
    _ŷ_ = _σ_(1 · 0.731 + 1 · 0.731 – 1.5) = _σ_(–0.39) = 0.404
    
    Because the prediction is closer to 0 than to 1, the point is misclassified.
    
2. Reducing the bias in the final node to anything less than 2 · 0.731 = 1.461 will do. For example, if this bias was 1.4, the prediction at the point (1,1) would be higher than 0.5. As an exercise, I encourage you to verify that this new neural network correctly predicts the labels for the remaining points.

## Chapter 11: Finding boundaries with style: Support vector machines and the kernel method

Exercise 11.1

(This exercise completes the calculation needed in the section “Distance error function.”)

Show that the distance between the lines with equations _w_1_x_1 + _w_2_x_2 + _b_ = 1 and _w_1_x_1 + _w_2_x_2 + _b_ = –1 is precisely ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E01.png).

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E00.png)

SOLUTION

First, let us call the lines as follows:

- _L_1 is the line with equation _w_1_x_1 + _w_2_x_2 + _b_ = 1.
- _L_2 is the line with equation _w_1_x_1 + _w_2_x_2 + _b_ = –1.

Note that we can rewrite the equation _w_1_x_1 + _w_2_x_2 + _b_ = 0 as ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E02.png) with slope ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E03.png). Any perpendicular to this line has slope ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E04.png). In particular, the line with equation ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E05.png) is perpendicular to both _L_1 and _L_2. We’ll call this line _L_3.

Next, we solve for the points of intersection of _L_3 with each of the lines _L_1 and _L_2. The point of intersection of _L_1 and _L_3 is the solution to the following equations:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E06.png)

We can plug the second equation into the first one, to obtain

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E07.png),

and subsequently solve for _x_1 to obtain

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E08.png).

Therefore, because every point in _L_2 has the form ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E09.png) the point of intersection of _L_1 and _L_3 is the point with coordinates ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E10.png).

A similar calculation will show that the point of intersection of _L_2 and _L_3 is the point with coordinates ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E11.png).

To find the distance between these two points, we can use the Pythagorean theorem. This distance is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_11_E12.png)

as desired.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/A-131.png)

Exercise 11.2

As we learned in exercise 5.3, it is impossible to build a perceptron model that mimics the XOR gate. In other words, it is impossible to fit the following dataset (with 100% accuracy) with a perceptron model:

|   |   |   |
|---|---|---|
|_x_1|_x_2|_y_|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

This is because the dataset is not linearly separable. An SVM has the same problem, because an SVM is also a linear model. However, we can use a kernel to help us out. What kernel should we use to turn this dataset into a linearly separable one? What would the resulting SVM look like?

hint Look at example 2 in the section “Using polynomial equations to your benefit,” which solves a very similar problem.

SOLUTION

Considering the polynomial kernel of degree two, we get the following dataset:

|   |   |   |   |   |   |
|---|---|---|---|---|---|
|_x_1|_x_2|_x_12|_x_1 _x_2|_x_22|_y_|
|0|0|0|0|0|0|
|0|1|0|0|1|1|
|1|1|1|0|0|1|
|1|1|1|1|1|0|

Several classifiers work on this modified dataset. For example, the one with equation _ŷ_ = _step_(_x_1 + _x_2 – 2_x_1_x_2 – 0.5) classifies the data correctly.

## Chapter 12: Combining models to maximize results: Ensemble learning

Exercise 12.1

A boosted strong learner _L_ is formed by three weak learners, _L_1, _L_2, and _L_3. Their weights are 1, 0.4, and 1.2, respectively. For a particular point, _L_1 and _L_2 predict that its label is positive, and _L_3 predicts that it’s negative. What is the final prediction the learner _L_ makes on this point?

SOLUTION

Because _L_1 and _L_2 predicted that the label is positive and _L_3 predicted that it is negative, the sum of votes is

1 + 0.4 – 1.2 = 0.2.

This result is positive, which means that the strong learner predicts that the label of this point is positive.

Exercise 12.2

We are in the middle of training an AdaBoost model on a dataset of size 100. The current weak learner classifies 68 out of the 100 data points correctly. What is the weight that we’ll assign to this learner in the final model?

SOLUTION

This weight is the log odds, or the natural logarithm of the odds. The odds are 68/32, because the classifier classifies 68 points correctly and misclassifies the remaining 32. Therefore, the weight assigned to this weak learner is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppA_12_E01.png)

## Chapter 13: Putting it all in practice: A real-life example of data engineering and machine learning

Exercise 13.1

The repository contains a file called test.csv. This is a file with more passengers on the _Titanic_, except it doesn’t have the Survived column.

1. Preprocess the data in this file as we did in this chapter.
2. Use any of the models to predict labels in this dataset. According to your model, how many passengers survived?
3. Comparing the performance of all the models in this chapter, how many passengers from the test set would you think actually survived?

SOLUTION

The solution is at the end of the following notebook: [https://github.com/luisguiserrano/manning/tree/master/Chapter_13_End_to_end_example](https://github.com/luisguiserrano/manning/tree/master/Chapter_13_End_to_end_example).

# Appendix B. The math behind gradient descent: Coming down a mountain using derivatives and slopes

In this appendix, we’ll go over the mathematical details of gradient descent. This appendix is fairly technical, and understanding it is not required to follow the rest of the book. However, it is here to provide a sense of completeness for the readers who wish to understand the inner workings of some of the core machine learning algorithms. The mathematics knowledge required for this appendix is higher than for the rest of the book. More specifically, knowledge of vectors, derivatives, and the chain rule is required.

In chapters 3, 5, 6, 10, and 11, we used gradient descent to minimize the error functions in our models. More specifically, we used gradient descent to minimize the following error functions:

- Chapter 3: the absolute and square error functions in a linear regression model
- Chapter 5: the perceptron error function in a perceptron model
- Chapter 6: the log loss in a logistic classifier
- Chapter 10: the log loss in a neural network
- Chapter 11: the classification (perceptron) error and the distance (regularization) error in an SVM

As we learned in chapters 3, 5, 6, 10, and 11, the error function measures how poorly the model is doing. Thus, finding the minimum value for this error function—or at least a really small value, even if it’s not the minimum—will be instrumental in finding a good model.

The analogy we used was that of descending a mountain—Mount Errorest, shown in figure B.1. The scenario is the following: You are somewhere on top of a mountain, and you’d like to get to the bottom of this mountain. It is very cloudy, so you can’t see far around you. The best bet you can have is to descend from the mountain one step at a time. You ask yourself , “If I were to take only one step, in which direction should I take it to descend the most?” You find that direction and take that step. Then you ask the same question again, and take another step, and you repeat the process many times. It is imaginable that if you always take the one step that helps you descend the most, that you must get to a low place. You may need a bit of luck to actually get to the bottom of the mountain, as opposed to getting stuck in a valley, but we’ll deal with this later in the section “Getting stuck on local minima.”

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-1.png)

Figure B.1 In the gradient descent step, we want to descend from a mountain called Mount Errorest.

Throughout the following sections we’ll describe the mathematics behind gradient descent and use it to help us train several machine learning algorithms by decreasing their error functions.

## Using gradient descent to decrease functions

The mathematical formalism of gradient descent follows: say you want to minimize the function _f_(_x_1, _x_2, …, _x_n) on the _n_ variables _x_1, _x_2, …, _x_n. We assume the function is continuous and differentiable over each of the _n_ variables.

We are currently standing at the point _p_ with coordinates (_p_1, _p_2, …, _p_n), and we wish to find the direction in which the function decreases the most, in order to take that step. This is illustrated in figure B.2. To find the direction in which the function decreases the most, we use the _gradient_ of the function. The gradient is the _n_-dimensional vector formed by the partial derivatives of _f_ with respect to each of the variables _x_1, _x_2, …, _x_n. This gradient is denoted as ∇_f_, as follows:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_01_E01.png)

The gradient is a vector that points in the direction of greatest growth, namely, the direction in which the function _increases_ the most. Thus, the negative of the gradient is the direction in which the function _decreases_ the most. This is the step we want to take. We determine the size of the step using the _learning rate_ we learned in chapter 3 and which we denote with _η_. The gradient descent step consists of taking a step of length _η_|∇_f|_ in the direction of the negative of the gradient ∇_f_. Thus, if our original point was _p_, after applying the gradient descent step, we obtain the point _p_ – _η_∇_f_. Figure B.2 illustrates the step that we’ve taken to decrease the function _f_.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-2.png)

Figure B.2 We were originally at the point _p_. We take a step in the direction of the negative of the gradient and end up at a new point. This is the direction in which the function decreases the most. (Source: Image created with the assistance of Grapher™ from Golden Software, LLC; [https://www.goldensoftware.com/products/grapher](https://www.goldensoftware.com/products/grapher)).

Now that we know how to take one step to slightly decrease the function, we can simply repeat this process many times to minimize our function. Thus, the pseudocode of the gradient descent algorithm is the following:

PSEUDOCODE FOR THE GRADIENT DESCENT ALGORITHM

**Goal**: To minimize the function _f_.

Hyperparameters:

- Number of epochs (repetitions) _N_
- Learning rate _η_

Process:

- Pick a random point _p_0.
- For _i_ = 0, …, _N_ – 1:
    - – Calculate the gradient ∇_f_(_p_i).
    - – Pick the point _p_i+1 = _p_i – _η_∇_f_(_p_i).

- End with the point _p_n.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-3.png)

Figure B.3 If we repeat the gradient descent step many times, we have a high chance of finding the minimum value of the function. In this figure, _p_1 represents the starting point and _p_n the point we have obtained using gradient descent. (Source: Image created with the assistance of Grapher™ from Golden Software, LLC; [https://www.goldensoftware.com/products/grapher](https://www.goldensoftware.com/products/grapher)).

Does this process _always_ find the minimum of the function? Unfortunately, no. Several problems may occur when trying to minimize a function using gradient descent, such as getting stuck at a local minimum (a valley). We’ll learn a very useful technique to deal with this problem in the section “Getting stuck on local minima.”

## Using gradient descent to train models

Now that we know how gradient descent helps us minimize (or at least, find small values for) a function, in this section we see how to use it to train some machine learning models. The models we’ll train follow:

- Linear regression (from chapter 3).
- Perceptron (from chapter 5).
- Logistic classifier (from chapter 6).
- Neural network (from chapter 10).
- Regularization (from chapters 4 and 11). This one is not a model, but we can still see the effects that a gradient descent step has on a model that uses regularization.

The way we use gradient descent to train a model is by letting _f_ be the corresponding error function of the model and using gradient descent to minimize _f_. The value of the error function is calculated over the dataset. However, as we saw in the sections “Do we train using one point at a time or many” in chapter 3, “Stochastic, mini-batch, and batch gradient descent” in chapter 6, and “Hyperparameters” in chapter 10, if the dataset is too large, we can speed up training by splitting the dataset into mini-batches of (roughly) the same size and, at each step, picking a different mini-batch on which to calculate the error function.

Here is some notation we’ll use in this appendix. Most of the terms have been introduced in chapters 1 and 2:

- The **size** of the dataset, or the number of rows, is _m_.
- The **dimension** of the dataset, or number of columns, is _n_.
- The dataset is formed by **features** and **labels**.
- The **features** are the _m_ vectors _x_i = (_x_1(i), _x_2(i), …, _x_n(i)) for _i_ = 1, 2, …, _m_.
- The **labels** _y_i, for _i_ = 1, 2, …, _m_.
- The **model** is given by the vector of _n_ weights _w_ = (_w_1, _w_2, …, _w_n) and the bias _b_ (a scalar) (except when the model is a neural network, which will have more weights and biases).
- The **predictions** _ŷ_, for _i_ = 1, 2, …, _m_.
- The **learning rate** of the model is _η_.
- The **mini-batches** of data are _B_1, _B_2, …, _B_l, for some number _l_. Each mini-batch has length _q_. The points in one mini-batch (for notational convenience) are denoted _x_(1), …, _x_(q), and the labels are _y_1, …, _y_q.

The gradient descent algorithm that we’ll use for training models follows:

GRADIENT DESCENT ALGORITHM FOR TRAINING MACHINE LEARNING MODELS

Hyperparameters:

- Number of epochs (repetitions) _N_
- Learning rate _η_

Process:

- Pick random weights _w_1, _w_2, …, _w_n and a random bias _b_.
- For _i_ = 0, …, _N_ – 1:
    - For each of the mini-batches _B_1, _B_2, …, _B_l.
        - Calculate the error function _f_(_w, b_) on that particular mini-batch.
        - Calculate the gradient ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E01.png)
        - Replace the weights and bias as follows:
            - _w_1 gets replaced by ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E02.png)
            - _b_ gets replaced by ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E03.png)

Throughout the following subsections, we will perform this process in detail for each of the following models and error functions:

- Linear regression model with the mean absolute error function (the following section)
- Linear regression model with the mean square error function (the following section)
- Perceptron model with the perceptron error function (the section “Using gradient descent to train classification models”)
- Logistic regression model with the log loss function (the section “Using gradient descent to train classification models”)
- Neural network with the log loss function (the section “Using gradient descent to train neural networks”)
- Models with regularization (the section “Using gradient descent for regularization”)

Using gradient descent to train linear regression models

In this section, we use gradient descent to train a linear regression model, using both of the error functions we’ve learned previously: the mean absolute error and the mean square error. Recall from chapter 3 that in linear regression, the predictions _ŷ_1,_ŷ_1, … , _ŷ_q are given by the following formula:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E04.png)

The goal of our regression model is to find the weights _w_1, …, _w_n, which produce predictions that are really close to the labels. Thus, the error function helps by measuring how far _ŷ_ is from _y_ for a particular set of weights. As we’ve seen in sections “The absolute error” and “The square error” in chapter 3, we have two different ways to calculate this distance. The first is the absolute value |_ŷ_ – _y_|, and the second one is the square of the difference (_y_ – _ŷ_)2. The first one gives rise to the mean absolute error, and the second one to the mean square error. Let’s study them separately.

TRAINING A LINEAR REGRESSION MODEL USING GRADIENT DESCENT TO REDUCE THE MEAN ABSOLUTE ERROR

In this subsection, we’ll calculate the gradient of the mean absolute error function and use it to apply gradient descent and train a linear regression model. The mean absolute error is a way to tell how far apart _ŷ_ and _y_ are. It was first defined in the section “The absolute error” in chapter 3, and its formula follows:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E05.png)

For convenience, we’ll abbreviate _MAE_(_w, b, x_, _y_) as _MAE_. To use gradient descent to reduce _MAE_, we need to calculate the gradient ∇_MAE_, which is the vector containing the _n_ + 1 partial derivatives of _MAE_ with respect to _w_1, …, _w_n, _b_,

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E06.png)

We’ll calculate these partial derivatives using the chain rule. First, notice that

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E07.png)

The derivative of _f_(_x_) = |_x_| is the sign function ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E08.png), which is +1 when _x_ is positive and –1 when x is negative (it is undefined at 0, but for convenience we can define it to be 0). Thus, we can rewrite the previous equation as

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E09.png)

To calculate this value, let’s focus on the final part of the equation, namely, the ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E10.png). Since ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E11.png), then

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E12.png).

This is because the derivative of _w_j with respect to _w_i, is 1 if _j_ = _i_ and 0 otherwise. Thus, replacing on the derivative, we get the following:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E13.png)

Using a similar analysis, we can calculate the derivative of _MAE_(_w_, _b_) with respect to _b_ to be

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E14.png)

The gradient descent step is the following:

GRADIENT DESCENT STEP:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E15.png)

Notice something interesting: if the mini-batch has size _q_ = 1 and consists only of the point _x_ = (_x_1, _x_2, …, _x_n) with label _y_ and prediction _ŷ_, then the step is defined as follows:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

- _w_j' = _w_j + _η_ sgn(_y_ – _ŷ_)_x_j
- _b_' = _b_ + _η_ sgn(_y_ – _ŷ_)

This is precisely the simple trick we used in the section “The simple trick” in chapter 3 to train our linear regression algorithm.

TRAINING A LINEAR REGRESSION MODEL USING GRADIENT DESCENT TO REDUCE THE MEAN SQUARE ERROR

In this subsection, we’ll calculate the gradient of the mean square error function and use it to apply gradient descent and train a linear regression model. The mean square error is another way to tell how far apart _ŷ_ and _y_ are. It was first defined in the section “The square error” in chapter 3 and its formula is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E16.png)

For convenience, we’ll abbreviate _MSE_(_w, b, x_, _y_) as _MSE_. To calculate the gradient ∇_MSE_, we can follow the same procedure as we did for the mean absolute error described earlier, with the exception that the derivative of _f_(_x_) = _x_2 is 2_x_. Therefore, the derivative of _MSE_ with respect to _w_j is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E17.png)

Similarly, the derivative of _MSE_(_w_, _b_) with respect to _b_ is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E18.png)

GRADIENT DESCENT STEP:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_03_E19.png)

Notice again that if the mini-batch has size _q =_ 1 and consists only of the point _x_ = (_x_1, _x_2, …, _x_n) with label _y_ and prediction _ŷ_, then the step is defined as follows:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

- _w_j' = _w_j + _η_(_y_ – _ŷ_)_x_j  
    
- _b__'_ = _b_ + _η_(_y_ – _ŷ_)

This is precisely the square trick we used in the section “The square trick” in chapter 3 to train our linear regression algorithm.

Using gradient descent to train classification models

In this section we learn how to use gradient descent to train classification models. The two models that we’ll train are the perceptron model (chapter 5) and the logistic regression model (chapter 6). Each one of them has its own error function, so we will develop them separately.

TRAINING A PERCEPTRON MODEL USING GRADIENT DESCENT TO REDUCE THE PERCEPTRON ERROR

In this subsection, we’ll calculate the gradient of the perceptron error function and use it to apply gradient descent and train a perceptron model. In the perceptron model, the predictions are _ŷ_1_,_ _ŷ_2_, …,_ _ŷ_q where each _ŷ_i is 0 or 1. To calculate the predictions, we first need to remember the step function _step_(_x_), introduced in chapter 5. This function takes as an input any real number _x_ and outputs 0 if _x_ < 0 and 1 if _x_ ≥ 0. Its graph is shown in figure B.4.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-41.png)

Figure B.4 The step function. For negative numbers it outputs 0, and for non-negative numbers it outputs 1.

The model gives each point a _score_. The score that the model with weights (_w_1, _w_2, …, _w_n) and bias _b_ gives to the point _x_(i) = (_x_1(i), _x_n(i), …, _x_n(i)) is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E01.png)

The predictions _ŷ_i are given by the following formula:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E02.png)

In other words, the prediction is 1 if the score is positive, and 0 otherwise.

The perceptron error function is called _PE_(_w, b, x_, _y_), which we’ll abbreviate as _PE_. It was first defined in the section “How to compare classifiers? The error function” in chapter 5. By construction, it is a large number if the model made a bad prediction, and a small number (in this case, actually 0) if the model made a good prediction. The error function is defined as follows.

- _PE_(_w, b, x, y_) = 0 if _ŷ_ = _y_
- _PE_(_w, b, x_, _y_) = |_score_(_w, b, x_)| if _ŷ_ _≠_ _y_

In other words, if the point is correctly classified, the error is zero. If the point is incorrectly classified, the error is the absolute value of the score. Thus, misclassified points with scores of low absolute value produce a low error, and misclassified points with scores of high absolute value produce a high error. This is because the absolute value of the score of a point is proportional to the distance between that point and the boundary. Thus, the points with low error are points that are close to the boundary, whereas points with high error are far from the boundary.

To calculate the gradient ∇_PE_, we can use the same rule as before. One thing we should notice is that the derivative of the absolute value function |_x_| is 1 when _x_ ≥ 0 and 0 when _x_ < 0. This derivative is undefined at 0, which is a problem with our calculations, but in practice, we can arbitrarily define it as 1 without any problems.

In chapter 10, we introduced the _ReLU_(_x_) (rectified linear unit) function, which is 0 when _x_ < 0 and _x_ when _x_ ≥ 0. Notice that there are two ways in which a point can be misclassified:

- If _y_ = 0 and _ŷ_ = 1. This means _score_(_w, b, x_) ≥ 0.
- If _y_ = 1 and _ŷ_ = 0. This means _score_(_w, b, x_) < 0.

Thus, we can conveniently rewrite the perceptron error as

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E03.png)

or in more detail, as

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E04.png)

Now we can proceed to calculate the gradient ∇_PE_ using the chain rule. An important observation that we’ll use and that the reader can verify is that the derivative of _ReLU_(_x_) is the step function _step_(_x_). This gradient is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E05.png)

which we can rewrite as

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E06.png)

This looks complicated, but it’s actually not that hard. Let’s analyze each summand from the right-hand side of the previous expression. Notice that _step_(_score_(_w, b, x_)) is 1 if and only if _score_(_w, b, x_) > 0, and otherwise, it is 0. This is precisely when _ŷ_ = 1. Similarly, _step_(–_score_(_w, b, x_)) is 1 if and only if _score_(_w, b, x_) < 0, and otherwise, it is 0. This is precisely when _ŷ_ = 0. Therefore

- If _ŷ_i = 0 and _y_i = 0:
    
    −_y_i _x_j(i)) _step_(−_score_(_w, b, x_)) + (1 − _y_i) _x_j(i) _step_(_score_(_w, b, x_)) = 0
    
- If _ŷ_i = 1 and _y_i = 1:
    
    −_y_i _x_j(i)) _step_(−_score_(_w, b, x_)) + (1 − _y_i) _x_j(i) _step_(_score_(_w, b, x_)) = 0
    
- If _ŷ_i = 0 and _y_i = 1:
    
    −_y_i _x_j(i)) _step_(−_score_(_w, b, x_)) + (1 − _y_i) _x_j(i) _step_(_score_(_w, b, x_)) = −_x_j(i))
    
- If _ŷ_i = 1 and _y_i = 0:
    
    −_y_i _x_j(i)) _step_(−_score_(_w, b, x_)) + (1 − _y_i) _x_j(i) _step_(_score_(_w, b, x_)) = _x_j(i))
    

This means that when calculating ∂_PE_/∂_x_j, only the summands coming from misclassified points will add value.

In a similar manner,

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E07.png)

Thus, the gradient descent step is defined as follows:

GRADIENT DESCENT STEP:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

- _w_j' = _w_j + _η_ Σiq=1 −_y_i _x_j(i)_step_(−_score_(_w, b, x_)) + (1 − _y_i) _x_j(i) _step_(_score_(_w, b, x_)), and
- _b_' = _b_ + _η_ Σiq=1 −_y_i_step_(−_score_(_w, b, x_)) + (1− _y_i)_step_(_score_(_w, b, x_))

And again, looking at the right-hand side of the previous expression  

- If _ŷ_i = 0 and _y_i = 0:
    
    −_y_i_step_(−_score_(_w, b, x_)) + (1− _y_i)_step_(_score_(_w, b, x_)) = 0
    
- If _ŷ_i = 1 and _y_i = 1:
    
    −_y_i_step_(−_score_(_w, b, x_)) + (1− _y_i)_step_(_score_(_w, b, x_)) = 0
    
- If _ŷ_i = 0 and _y_i = 1:
    
    −_y_i_step_(−_score_(_w, b, x_)) + (1− _y_i)_step_(_score_(_w, b, x_)) = −_1_
    
- If _ŷ_i = 1 and _y_i = 0:
    
    −_y_i_step_(−_score_(_w, b, x_)) + (1− _y_i)_step_(_score_(_w, b, x_)) = _1_
    

This all may not mean very much, but one can code this to calculate all the entries of the gradient. Notice again that if the mini-batch has size _q_ = 1 and consists only of the point _x_ = (_x_1, _x_2, …, _x_n) with label _y_ and prediction _ŷ_, then the step is defined as follows:

GRADIENT DESCENT STEP:

- If the point is correctly classified, don’t change _w_ and _b_.
- If the point has label _y_ = 0 and is classified as _ŷ_ = 1:
    - Replace _w_ by _w_' = _w_ – _η__x_.
    - Replace _b_ by _b_' = _w_ – _η_.
- If the point has label _y_ = 1 and is classified as _ŷ_ = 0:
    - Replace _w_ by _w_' = _w_ + _η__x_.
    - Replace _b_ by _b_' = _w_ + _η_.

Note that this is precisely the perceptron trick described in the section “The perceptron trick” in chapter 5.

TRAINING A LOGISTIC REGRESSION MODEL USING GRADIENT DESCENT TO REDUCE THE LOG LOSS

In this subsection, we’ll calculate the gradient of the log loss function and use it to apply gradient descent and train a logistic regression model. In the logistic regression model, the predictions are _ŷ_1, _ŷ_2, …, _ŷ_q where each _ŷ_i is some real number in between 0 and 1. To calculate the predictions, we first need to remember the sigmoid function _σ_(_x_), introduced in chapter 6. This function takes as an input any real number _x_ and outputs some number between 0 and 1. If _x_ is a large positive number, then _σ_(_x_) is close to 1. If _x_ is a large negative number, then _σ_(_x_) is close to 0. The formula for the sigmoid function is

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E08.png)

The graph of _σ_(_x_) is illustrated in figure B.5.

The predictions of the logistic regression model are precisely the output of the sigmoid function, namely, for _i_ = 1, 2, …, _q_ they are defined as follows:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_04_E09.png)

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-51.png)

Figure B.5 The sigmoid function always outputs a number between 0 and 1. The output for negative numbers is close to 0, and the output for positive numbers is close to 1.

The log loss is denoted as _LL_(_w, b, x_, _y_), which we’ll abbreviate as _LL_. This error function was first defined in the section “The dataset and the predictions” in chapter 6. It is similar to the perceptron error function because by construction, it is a large number if the model made a bad prediction and a small number if the model made a good prediction. The log loss function is defined as

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E01.png)

We can proceed to calculate the gradient ∇_LL_ using the chain rule. Before this, let’s note that the derivative of the sigmoid function can be written as _σ__'_(_x_) = _σ_(_x_)|1 – _σ_(_x_)|. The details on the last calculation can be worked out using the quotient rule for differentiation, and they are left to the reader. Using this, we can calculate the derivative of _ŷ_i with respect to _w_j. Because _ŷ_i = _σ_(Σin=1_w_j_x_j(i) + _b_)), then by the chain rule,

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E02.png)

Now, on to develop the log loss. Using the chain rule again, we get

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E03.png)

And by the previous calculation for ∂_ŷ_i/∂_w_j,

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E04.png)

Simplifying, we get

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E05.png)

which simplifies even more as

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E06.png)

Similarly, taking the derivative with respect to _b_, we get

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_05_E07.png)

Therefore, the gradient descent step becomes the following:

GRADIENT DESCENT STEP:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

- _w_' = _w_ + _η_Σiq=1(_y_i – _ŷ_i)_x_(i)
- _b_' = _b_ + _η_Σiq=1(_y_i – _ŷ_i)

Notice that when the mini-batches are of size 1, the gradient descent step becomes the following:

Replace (_w_, _b_) by (_w__'_, _b__'_), where

- _w_' = _w_ + _η_(_y_i – _ŷ_i)_x_(i)
- _b_' = _b_ + _η_(_y_i – _ŷ_i)

This is precisely the logistic regression trick we learned in the section “How to find a good logistic classifier?” in chapter 6.

Using gradient descent to train neural networks

In the section “Backpropagation” in chapter 10, we went over backpropagation—the process of training a neural network. This process consists of repeating a gradient descent step to minimize the log loss. In this subsection, we see how to actually calculate the derivatives to perform this gradient descent step. We’ll perform this process in a neural network of depth 2 (one input layer, one hidden layer, and one output layer), but the example is big enough to show how these derivatives are calculated in general. Furthermore, we’ll apply gradient descent on the error for only one point (in other words, we’ll do stochastic gradient descent). However, I encourage you to work out the derivatives for a neural network of more layers, and using mini-batches of points (mini-batch gradient descent).

In our neural network, the input layer consists of _m_ input nodes, the hidden layer consists of _n_ hidden nodes, and the output layer consists of one output node. The notation in this

subsection is different than in the other ones, for the sake of simplicity, as follows (and illustrated in figure B.6):

- The input is the point with coordinates _x_1, _x_2, …, _x_m.
- The first hidden layer has weights _V_ij and biases _b_j, for _i_ = 1, 2, …, _m_ and _j_ = 1, 2, …, _n_.
- The second hidden layer has weights _W_j for _j_ = 1, 2, …, _n_, and bias _c_.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-64.png)

Figure B.6 The process of calculating a prediction using a neural network with one hidden layer and sigmoid activation functions

The way the output is calculated is via the following two equations:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ea01.png)

To ease the calculation of the derivatives, we use the following helper variables _r_j and _s_:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ea02.png)

In that way, we can calculate the following partial derivatives (recalling that the derivative of the sigmoid function is _σ__'_(_x_) = _σ__'_(_x_)[1 – _σ_(_x_)] and that the log loss is _L_(_y_, _ŷ_) = – _y ln_(_ŷ_) – (1 – _y_)_ln_(1 – _ŷ_)—we’ll call it _L_ for convenience):

1. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb01.png)
2. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb02.png)
3. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb03.png)
4. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb04.png)
5. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb05.png)
6. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb06.png)
    
    To simplify our calculations, notice that if we multiply equations 1 and 2 and use the chain rule, we get
    
7. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb07.png)
    
    Now, we can use the chain rule and equations 3–7 to calculate the derivatives of the log loss with respect to the weights and biases as follows:
    
8. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb08.png)
9. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb09.png)
10. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb10.png)
11. ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Eb11.png)

Using the previous equations, the gradient descent step is the following:

GRADIENT DESCENT STEP FOR NEURAL NETWORKS:

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ec01a.png)

The previous equations are quite complicated, and so are the equations of backpropagation for neural networks of even more layers. Thankfully, we can use PyTorch, TensorFlow, and Keras to train neural networks without having to calculate all the derivatives.

## Using gradient descent for regularization

In the section “Modifying the error function to solve our problem” in chapter 4, we learned regularization as a way to decrease overfitting in machine learning models. Regularization consists of adding a regularization term to the error function, which helps reduce overfitting. This term could be the L1 or the L2 norm of the polynomial used in the model. In the section “Techniques for training neural networks” in chapter 10, we learned how to apply regularization to train neural networks by adding a similar regularization term. Later, in the section “Distance error function” in chapter 11, we learned the distance error function for SVMs, which ensured that the two lines in the classifier stayed close to each other. The distance error function had the same form of the L2 regularization term.

However, in the section “An intuitive way to see regularization” in chapter 4, we learned a more intuitive way to see regularization. In short, every gradient descent step that uses regularization is decreasing the values of the coefficients of the model by a slight amount. Let’s look at the math behind this phenomenon.

For a model with weights _w_1, _w_2, …, _w_n, the regularization terms were the following:

- L1 regularization: _W_1 = |_w_1| + |_w_2| + … + |_w_n|
- L2 regularization: _W_2 = _w_12 + _w_22 + … + _w_n2

Recall that in order to not alter the coefficients too drastically, the regularization term is multiplied by a regularization parameter _l_. Thus, when we apply gradient descent, the coefficients are modified as follows:

- L1 regularization: _w_i is replaced by _w_i – ∇ _W_1
- L2 regularization: _w_i is replaced by _w_i – ∇ _W_2

Where ∇ denotes the gradient of the regularization term. In other words, ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ed01.png), . Since ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ed02.png), and ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ed03.png) then the gradient descent step is the following:

GRADIENT DESCENT STEP FOR REGULARIZATION:

- L1 regularization: Replace _a_i by ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ed04.png)
- L2 regularization: Replace _a_i by ![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/AppB_06_Ed05.png)

Notice that this gradient descent step always reduces the absolute value of the coefficient _a_i. In L1 regularization, we are subtracting a small value from _a_i if it is _a_i positive and adding a small value if it is negative. In L2 regularization, we are multiplying _a_i by a number that is slightly smaller than 1.

## Getting stuck on local minima: How it happens, and how we solve it

As was mentioned at the beginning of this appendix, the gradient descent algorithm doesn’t necessarily find the minimum value of the function. As an example, look at figure B.7. Let’s say that we want to find the minimum of the function in this figure using gradient descent. Because the first step in gradient descent is to start on a random point, we’ll start at the point labeled “Starting point.”

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-710.png)

Figure B.7 We’re standing at the point labeled “Starting point.” The minimum value of the function is the point labeled “Minimum.” Will we be able to reach this minimum using gradient descent?

Figure B.8 shows the path that the gradient descent algorithm will take to find the minimum. Note that it succeeds in finding the closest local minimum to that point, but it completely misses the global minimum on the right.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-810.png)

Figure B.8 Unfortunately, gradient descent didn’t help us find the minimum value of this function. We did manage to go down, but we got stuck at a local minimum (valley). How can we solve this problem?

How do we solve this problem? We can use many techniques to fix this, and in this section, we learn a common one called _random restart_. The solution is to simply run the algorithm several times, always starting at a different random point, and picking the minimum value that was found overall. In figure B.9, we use random restart to find the global minimum on a function (note that this function is defined only on the interval pictured, and thus, the lowest value in the interval is indeed the global minimum). We have chosen three random starting points, one illustrated by a circle, one by a square, and one by a triangle. Notice that if we use gradient descent on each of these three points, the square manages to find the global minimum of the function.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/B-91.png)

Figure B.9 The random restart technique illustrated. The function is defined only on this interval, with three valleys, and the global minimum located in the second valley. Here we run the gradient descent algorithm with three different starting points: the circle, the square, and the triangle. Notice that the square managed to find the global minimum of the function.

This method is still not guaranteed to find the global minimum, because we may be unlucky and pick only points that get stuck in valleys. However, with enough random starting points, we have a much higher chance of finding the global minimum. And even if we can’t find the global minimum, we may still be able to find a good enough local minimum that will help us train a good model.

# Appendix C. References

These references can also be found in [https://serrano.academy/grokking-machine-learning/](https://serrano.academy/grokking-machine-learning/).

## General references

- GitHub repository: [www.github.com/luisguiserrano/manning](http://www.github.com/luisguiserrano/manning)
- YouTube videos: [www.youtube.com/c/LuisSerrano](http://www.youtube.com/c/LuisSerrano)
- General information: [https://serrano.academy](https://serrano.academy/)
- Book information: [https://serrano.academy/grokking-machine-learning](https://serrano.academy/grokking-machine-learning)

## Courses

- Udacity machine learning nanodegree: [http://mng.bz/4KE5](http://mng.bz/4KE5)
- Coursera machine learning course: [https://www.coursera.org/learn/machine-learning](https://www.coursera.org/learn/machine-learning)
- Coursera machine learning specialization (University of Washington): [http://mng.bz/Xryl](http://mng.bz/Xryl)
- End-to-end machine learning: [https://end-to-end-machine-learning.teachable.com/courses](https://end-to-end-machine-learning.teachable.com/courses)

## Blogs and YouTube channels

- Machine learning videos by Brandon Rohrer: [https://www.youtube.com/user/BrandonRohrer](https://www.youtube.com/user/BrandonRohrer)
- StatQuest with Josh Starmer: [https://www.youtube.com/user/joshstarmer](https://www.youtube.com/user/joshstarmer)
- Chris Olah blog: [https://colah.github.io/](https://colah.github.io/)
- Jay Alammar blog: [https://jalammar.github.io/](https://jalammar.github.io/)
- Alexis Cook blog: [https://alexisbcook.github.io/](https://alexisbcook.github.io/)
- Dhruv Parthasarathy blog: [https://medium.com/@dhruvp](https://medium.com/@dhruvp)
- 3Blue1Brown: [http://youtube.com/c/3blue1brown](http://youtube.com/c/3blue1brown)
- Machine Learning Mastery: [https://machinelearningmastery.com](https://machinelearningmastery.com/)
- Andrej Karpathy blog: [http://karpathy.github.io/](http://karpathy.github.io/)

## Books

- _Pattern Recognition and Machine Learning_, by Christopher Bishop: [http://mng.bz/g1DZ](http://mng.bz/g1DZ)

## Chapter 1

Videos

- General machine learning videos: [https://serrano.academy/general-machine-learning/](https://serrano.academy/general-machine-learning/)
- A friendly introduction to machine learning video: [www.youtube.com/watch?v=IpGxLWOIZy4](https://www.youtube.com/watch?v=IpGxLWOIZy4)
- Monty Python spam sketch: [www.youtube.com/watch?v=zLih-WQwBSc](https://www.youtube.com/watch?v=zLih-WQwBSc)

## Chapter 2

Videos

- Supervised machine learning videos: [https://serrano.academy/linear-models/](https://serrano.academy/linear-models/)
- Unsupervised machine learning videos: [https://serrano.academy/unsupervised-learning/](https://serrano.academy/unsupervised-learning/)
- Generative machine learning videos: [https://serrano.academy/generative-models](https://serrano.academy/generative-models)
- Reinforcement learning videos: [https://serrano.academy/reinforcement-learning](https://serrano.academy/reinforcement-learning)
- Deep learning videos: [https://serrano.academy/neural-networks](https://serrano.academy/neural-networks)

Books

- _Grokking Deep Reinforcement Learning,_ by Miguel Morales: [http://mng.bz/5Zy4](http://mng.bz/5Zy4)

Courses

- UCL course on reinforcement learning, by David Silver: [https://www.davidsilver.uk/teaching/](https://www.davidsilver.uk/teaching/)
- Udacity Deep Reinforcement Learning Nanodegree Program: [http://mng.bz/6mMG](http://mng.bz/6mMG)

## Chapter 3

Code

- GitHub repository: [http://mng.bz/o8lN](http://mng.bz/o8lN)

Datasets

- Hyderabad housing dataset:
    - Creator: Ruchi Bhatia
    - Date: 2020/08/27
    - Version: 4
    - Retrieved from [http://mng.bz/nrdv](http://mng.bz/nrdv)
    - License: CC0: Public Domain

Videos

- Linear regression video: [http://mng.bz/v4Rx](http://mng.bz/v4Rx)
- Polynomial regression video: [https://www.youtube.com/watch?v=HmmkA-EFaW0](https://www.youtube.com/watch?v=HmmkA-EFaW0)

## Chapter 4

Code

- GitHub repository: [http://mng.bz/4KXB](http://mng.bz/4KXB)

Videos

- Machine learning: Testing and error metrics: [https://www.youtube.com/watch?v=aDW44NPhNw0](https://www.youtube.com/watch?v=aDW44NPhNw0)
- Lasso (L1) regression (StatQuest): [https://www.youtube.com/watch?v=NGf0voTMlcs](https://www.youtube.com/watch?v=NGf0voTMlcs)
- Ridge (L2) regression (StatQuest): [https://www.youtube.com/watch?v=Q81RR3yKn30](https://www.youtube.com/watch?v=Q81RR3yKn30)

## Chapter 5

Code

- GitHub repository: [http://mng.bz/Qqpm](http://mng.bz/Qqpm)

Videos

- Logistic regression and the perceptron algorithm video: [https://www.youtube.com/watch?v=jbluHIgBmBo](https://www.youtube.com/watch?v=jbluHIgBmBo)
- Hidden Markov models video: [https://www.youtube.com/watch?v=kqSzLo9fenk](https://www.youtube.com/watch?v=kqSzLo9fenk)

## Chapter 6

Code

- [GitHub repository:](https://github.com/luisguiserrano/manning/tree/master/Chapter_6_Logistic_Regression) [http://mng.bz/Xr9Y](http://mng.bz/Xr9Y)

Datasets

- IMDB movie extensive reviews dataset
    - Creator: Stefano Leone
    - Date: 2019/11/24
    - Version: 2
    - Retrieved from [https://www.kaggle.com/stefanoleone992/imdb-extensive-dataset](https://www.kaggle.com/stefanoleone992/imdb-extensive-dataset)
    - License: CC0: Public Domain

Videos

- Logistic regression and the perceptron algorithm video: [https://www.youtube.com/watch?v=jbluHIgBmBo](https://www.youtube.com/watch?v=jbluHIgBmBo)
- Cross entropy (StatQuest): [https://www.youtube.com/watch?v=6ArSys5qHAU](https://www.youtube.com/watch?v=6ArSys5qHAU)
- Cross entropy (Aurélien Géron): [https://www.youtube.com/watch?v=ErfnhcEV1O8](https://www.youtube.com/watch?v=ErfnhcEV1O8)

## Chapter 7

Videos

- Machine learning: Testing and error metrics: [https://www.youtube.com/watch?v=aDW44NPhNw0](https://www.youtube.com/watch?v=aDW44NPhNw0)

## Chapter 8

Code

- GitHub repository: [http://mng.bz/yJRJ](http://mng.bz/yJRJ)

Datasets

- Spam filter dataset
    - Creator: Karthik Veerakumar
    - Date: 17/07/14
    - Version: 1
    - Retrieved from [https://www.kaggle.com/karthickveerakumar/spam-filter](https://www.kaggle.com/karthickveerakumar/spam-filter)
    - Visibility: Public

Videos

- Naive Bayes: [https://www.youtube.com/watch?v=Q8l0Vip5YUw](https://www.youtube.com/watch?v=Q8l0Vip5YUw)

## Chapter 9

Code

- GitHub repository: [http://mng.bz/MvM2](http://mng.bz/MvM2)

Datasets

- Admissions dataset
    - Creator: Mohan S. Acharya
    - Date: 2018/03/03  
        
    - Version: 2
    - Retrieved from [http://mng.bz/aZlJ](http://mng.bz/aZlJ)
    - Article: Mohan S. Acharya, Asfia Armaan, and Aneeta S Antony, “A Comparison of Regression Models for Prediction of Graduate Admissions”, IEEE International Conference on Computational Intelligence in Data Science (2019).
    - License: CC0: Public Domain

Videos

- Decision trees (StatQuest): [https://www.youtube.com/watch?v=7VeUPuFGJHk](https://www.youtube.com/watch?v=7VeUPuFGJHk)
- Regression decision trees (StatQuest): [https://www.youtube.com/watch?v=g9c66TUylZ4](https://www.youtube.com/watch?v=g9c66TUylZ4)
- Decision trees (Brandon Rohrer): [https://www.youtube.com/watch?v=9w16p4QmkAI](https://www.youtube.com/watch?v=9w16p4QmkAI)
- Gini impurity index: [https://www.youtube.com/watch?v=u4IxOk2ijSs](https://www.youtube.com/watch?v=u4IxOk2ijSs)
- Shannon entropy and information gain: [https://www.youtube.com/watch?v=9r7FIXEAGvs](https://www.youtube.com/watch?v=9r7FIXEAGvs)

Blog posts

- Shannon entropy, information gain, and picking balls from buckets: [http://mng.bz/g1lR](http://mng.bz/g1lR)

## Chapter 10

Code

- GitHub repository: [http://mng.bz/ePAJ](http://mng.bz/ePAJ)

Datasets

- MNIST dataset. Deng, L. “The MNIST Database of Handwritten Digit images for Machine Learning Research.” _IEEE Signal Processing Magazine_ 29, no. 6 (2012): 141–42. [http://yann.lecun.com/exdb/mnist/](http://yann.lecun.com/exdb/mnist/)
- Hyderabad housing dataset (see references for [chapter 3](https://www.youtube.com/watch?v=BR9h47Jtqyw))

Videos

- Deep learning and neural networks: [https://www.youtube.com/watch?v=BR9h47Jtqyw](https://www.youtube.com/watch?v=BR9h47Jtqyw)
- Convolutional neural networks: [https://www.youtube.com/watch?v=2-Ol7ZB0MmU](https://www.youtube.com/watch?v=2-Ol7ZB0MmU)
- Recurrent neural networks: [https://www.youtube.com/watch?v=UNmqTiOnRfg](https://www.youtube.com/watch?v=UNmqTiOnRfg)
- How neural networks work (Brandon Rohrer): [https://www.youtube.com/watch?v=ILsA4nyG7I0](https://www.youtube.com/watch?v=ILsA4nyG7I0)
- Recurrent neural networks (RNN) and long short-term memory (LSTM) (Brandon Rohrer): [https://www.youtube.com/watch?v=WCUNPb-5EYI](https://www.youtube.com/watch?v=WCUNPb-5EYI)

Books

- _Grokking Deep Learning_, by Andrew Trask: [https://www.manning.com/books/grokking-deep-learning](https://www.manning.com/books/grokking-deep-learning)
- _Deep Learning_, by Ian Goodfellow, Yoshua Bengio, and Aaron Courville: [https://www.deeplearningbook.org/](https://www.deeplearningbook.org/)

Courses

- Udacity deep learning course: [http://mng.bz/p9lP](http://mng.bz/p9lP)

Blog posts

- “[Using Transfer Learning to Classify Images with Keras](https://alexisbcook.github.io/2017/using-transfer-learning-to-classify-images-with-keras/)”, by Alexis Cook: [http://mng.bz/OQgP](http://mng.bz/OQgP)
- “[Global Average Pooling Layers for Object Localization](https://alexisbcook.github.io/2017/global-average-pooling-layers-for-object-localization/)”, by Alexis Cook: [http://mng.bz/Ywj7](http://mng.bz/Ywj7)
- “[A Brief History of CNNs in Image Segmentation: From R-CNN to Mask R-CNN](https://blog.athelas.com/a-brief-history-of-cnns-in-image-segmentation-from-r-cnn-to-mask-r-cnn-34ea83205de4?gi=baf5b651aa4f)”, by Dhruv Parthasarathy: [http://mng.bz/GOnN](http://mng.bz/GOnN)
- “[Neural networks, Manifolds, and Topology](https://colah.github.io/posts/2014-03-NN-Manifolds-Topology/)”, by Chris Olah: [http://mng.bz/zERZ](http://mng.bz/zERZ)
- “[Understanding LSTM Networks](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)”, by Chris Olah: [http://mng.bz/01nz](http://mng.bz/01nz)
- “[How GPT3 Works: Visualizations and Animations](https://jalammar.github.io/how-gpt3-works-visualizations-animations/)”, by Jay Alammar: [http://mng.bz/KoXn](http://mng.bz/KoXn)
- “[How to Configure the Learning Rate When Training Deep Learning Neural Networks](https://machinelearningmastery.com/learning-rate-for-deep-learning-neural-networks)”, by Jason Brownlee: [http://mng.bz/9ae8](http://mng.bz/9ae8)
- “[Setting the Learning Rate of Your Neural Network](https://www.jeremyjordan.me/nn-learning-rate/)”, by Jeremy Jordan: [http://mng.bz/WBKX](http://mng.bz/WBKX)
- “[Selecting the Best Architecture for Artificial Neural Networks,” by Ahmed Gad:](https://heartbeat.fritz.ai/selecting-the-best-architecture-for-artificial-neural-networks-7b051f775b4) [http://mng.bz/WBKX](http://mng.bz/WBKX)
- “[A Recipe for Training Neural Networks](http://karpathy.github.io/2019/04/25/recipe/)”, by Andrej Karpathy[:](https://playground.tensorflow.org/) [http://mng.bz/80gg](http://mng.bz/80gg)

Tools

- TensorFlow playground: [https://playground.tensorflow.org/](https://playground.tensorflow.org/)

## Chapter 11

Code

- [GitHub repository](https://github.com/luisguiserrano/manning/tree/master/Chapter_11_Support_Vector_Machines): [http://mng.bz/ED6r](http://mng.bz/ED6r)

Videos

- Support vector machines: [https://www.youtube.com/watch?v=Lpr__X8zuE8](https://www.youtube.com/watch?v=Toet3EiSFcM)
- The polynomial kernel (StatQuest): [https://www.youtube.com/watch?v=Toet3EiSFcM](https://www.youtube.com/watch?v=Qc5IyLW_hns)
- The radial (RBF) kernel (StatQuest): [https://www.youtube.com/watch?v=Qc5IyLW_hns](https://www.youtube.com/watch?v=Qc5IyLW_hns)

Blog posts

- “[Kernels and Feature Maps: Theory and Intuition](https://xavierbourretsicotte.github.io/Kernel_feature_map.html)”, by Xavier Bourret Sicotte: [http://mng.bz/N4aX](http://mng.bz/N4aX)

## Chapter 12

Code

- [GitHub repository:](https://github.com/luisguiserrano/manning/tree/master/Chapter_12_Ensemble_Methods) [http://mng.bz/DK50](http://mng.bz/DK50)

Videos

- Random forests (StatQuest): [https://www.youtube.com/watch?v=J4Wdy0Wc_xQ](https://www.youtube.com/watch?v=LsK-xG1cLYA)
- AdaBoost (StatQuest): [https://www.youtube.com/watch?v=LsK-xG1cLYA](https://www.youtube.com/watch?v=3CC4N4z3GJc)
- Gradient boosting (StatQuest): [https://www.youtube.com/watch?v=3CC4N4z3GJc](https://www.youtube.com/watch?v=OtD8wVaFm6E)
- XGBoost (StatQuest): [https://www.youtube.com/watch?v=OtD8wVaFm6E](https://www.youtube.com/watch?v=OtD8wVaFm6E)

Articles and blog posts

- “A Decision-Theoretic Generalization of Online Learning and an Application to Boosting,” by Yoav Freund and Robert Shapire, _Journal of Computer and System Sciences_, 55, 119–139. [http://mng.bz/l9Bz](http://mng.bz/l9Bz)
- “Explaining AdaBoost,” by Robert Schapire: [http://rob.schapire.net/papers/explaining -adaboost.pdf](http://rob.schapire.net/papers/explaining-adaboost.pdf)
- “XGBoost: A Scalable Tree Boosting System,” by Tiani Chen and Carlos Guestrin. KDD ’16: Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining, August 2016, 785–794. [https://doi.org/10.1145/2939672.2939785](https://doi.org/10.1145/2939672.2939785)
- [“Winning the Netflix Prize](https://blog.echen.me/2011/10/24/winning-the-netflix-prize-a-summary/): A Summary”, by Edwin Chen: [http://mng.bz/B1jq](http://mng.bz/B1jq)

## Chapter 13

[](https://github.com/luisguiserrano/manning/tree/master/Chapter_13_End_to_end_example)Code

- [GitHub repository](https://github.com/luisguiserrano/manning/tree/master/Chapter_13_End_to_end_example): [http://mng.bz/drlz](http://mng.bz/drlz)

Datasets

- Titanic dataset:
    - Retrieved from [https://www.kaggle.com/c/titanic/data](https://www.kaggle.com/c/titanic/data)

## Graphics and image icons

Graphics and image icons were provided by the following creators from [flaticon.com](http://flaticon.com/):

- IFC—hiker, mountain range: Freepik; flag: Good Ware
- Preface—musician: photo3idea_studio; mathematician: Freepik
- Figure 1.2—person: Eucalyp; computer: Freepik
- Figure 1.4—person: ultimatearm
- Figures 1.5, 1.6, 1.7, 1.8, 1.9—person on computer: Freepik
- Figures 2.1, 2.2, 2.3, 2.4—dog: Freepik; cat: Smashicons

- Figures 2.2, 2.3, 2.4—factory: DinosoftLabs
- Figures 2.5, 2.6—envelope: Freepik
- Figure 2.7—house: Vectors Market
- Figure 2.8—map point: Freepik
- Figures 2.11, 2.12—robot, mountain: Freepik; dragon: Eucalyp; Treasure: Smashicons
- Figures 3.3, 3.4, 3.5—house: Vectors Market
- Figures 3.20, 3.21, 3.22—hiker, mountain range: Freepik; flag, bulb: Good Ware
- Figure 4.1—Godzilla: Freepik; swatter: Smashicons; fly: Eucalyp; bazooka: photo3idea_studio
- Figure 4.6—house: Vectors Market; bandage, shingles: Freepik; titanium rod: Vitaly Gorbachev
- Figures 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.10, 5.11, 5.12, 5.13, 5.16, 5.17, 5.18, 5.19, 5.20, 5.21, 5.22, 5.23, 5.24, 6.1, 6.4, 6.5, 6.6, 10.2, 10.3—happy face, sad face, neutral face: Vectors Market
- Figures 5.15, 5.16—hiker: Freepik; bulb: Good Ware
- Figure 7.11 (in the exercises)—various animals: surang; book: Good Ware; clock, strawberry, planet, soccer ball: Freepik; video: monkik
- Figure 8.4—chef hat: Vitaly Gorbachev; wheat, bread: Freepik
- Figures 8.5, 8.6, 8.7, 8.8, 8.9, 8.10, 8.11, 8.12, 8.13—envelope: Smashicons
- Figures 9.6, 9.7, 9.8, 9.9, 9.12, 9.13, 9.15, 9.16, 9.17—atom: mavadee; beehive: Smashicons; chess knight: Freepik
- Figures 9.13, 9.17—handbag: Gregor Cresnar
- Joke in chapter 10—arms with muscle: Freepik
- Joke in chapter 11—corn, chicken: Freepik; chicken: monkik
- Joke in chapter 12—shark, fish: Freepik
- Figure 12.1—big robot: photo3idea_studio; small robots: Freepik
- Joke in chapter 13—ship: Freepik