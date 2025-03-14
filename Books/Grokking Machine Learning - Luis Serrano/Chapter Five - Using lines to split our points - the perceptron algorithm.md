# 5 Using lines to split our points: The perceptron algorithm

In this chapter

- what is classification
- sentiment analysis: how to tell if a sentence is happy or sad using machine learning
- how to draw a line that separates points of two colors
- what is a perceptron, and how do we train it
- coding the perceptron algorithm in Python and Turi Create

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/CH05_F01_Serrano_Text.png)

In this chapter, we learn a branch of machine learning called _classification_. Classification models are similar to regression models, in that their aim is to predict the labels of a dataset based on the features. The difference is that regression models aim to predict a number, whereas classification models aim to predict a state or a category. Classification models are often called _classifiers_, and we’ll use the terms interchangeably. Many classifiers predict one of two possible states (often yes/no), although it is possible to build classifiers that predict among a higher number of possible states. The following are popular examples of classifiers:

- A recommendation model that predicts whether a user will watch a certain movie
- An email model that predicts whether an email is spam or ham
- A medical model that predicts whether a patient is sick or healthy
- An image-recognition model that predicts whether an image contains an automobile, a bird, a cat, or a dog
- A voice recognition model that predicts whether the user said a particular command

Classification is a popular area in machine learning, and the bulk of the chapters in this book (chapters 5, 6, 8, 9, 10, 11, and 12) talk about different classification models. In this chapter, we learn the _perceptron_ model, also called the _perceptron classifier_, or simply the _perceptron_. A perceptron is similar to a linear regression model, in that it uses a linear combination of the features to make a prediction and is the building block of neural networks (which we learn in chapter 10). Furthermore, the process of training a perceptron is similar to that of training a linear regression model. Just as we did in chapter 3 with the linear regression algorithm, we develop the perceptron algorithm in two ways: using a trick that we can iterate many times, and defining an error function that we can minimize using gradient descent.

The main example of classification models that we learn in this chapter is _sentiment analysis_. In sentiment analysis, the goal of the model is to predict the sentiment of a sentence. In other words, the model predicts whether the sentence is happy or sad. For example, a good sentiment analysis model can predict that the sentence “I feel wonderful!” is a happy sentence, and that the sentence “What an awful day!” is a sad sentence.

Sentiment analysis is used in many practical applications, such as the following:

- When a company analyzes the conversations between customers and technical support, to evaluate the quality of the conversation
- When analyzing the tone of a brand’s digital presence, such as comments on social media or reviews related to its products
- When a social platform like Twitter analyzes the overall mood of a certain population after an event
- When an investor uses public sentiment toward a company to predict its stock price

How could we build a sentiment analysis classifier? In other words, how could we build a machine learning model that takes a sentence as an input and, as output, tells us whether the sentence is happy or sad. This model can make mistakes, of course, but the idea is to build it in such a way that it makes as few mistakes as possible. Let’s put down the book for a couple of minutes and think of how we would go about building this type of model.

Here is an idea. Happy sentences tend to contain happy words, such as _wonderful_, _happy_, or _joy_, whereas sad sentences tend to contain sad words, such as _awful_, _sad_, or _despair_. A classifier can consist of a “happiness” score for every single word in the dictionary. Happy words can be given positive scores, and sad words can be given negative scores. Neutral words such as _the_ can be given a score of zero. When we feed a sentence into our classifier, the classifier simply adds the scores of all the words in the sentence. If the result is positive, then the classifier concludes that the sentence is happy. If the result is negative, then the classifier concludes that the sentence is sad. The goal now is to find scores for all the words in the dictionary. For this, we use machine learning.

The type of model we just built is called a _perceptron model_. In this chapter, we learn the formal definition of a perceptron and how to train it by finding the perfect scores for all the words so that our classifier makes as few mistakes as possible.

The process of training a perceptron is called the _perceptron algorithm_, and it is not that different from the linear regression algorithm we learned in chapter 3. Here is the idea of the perceptron algorithm: To train the model, we first need a dataset containing many sentences together with their labels (happy/sad). We start building our classifier by assigning random scores to all the words. Then we go over all the sentences in our dataset several times. For every sentence, we slightly tweak the scores so that the classifier improves the prediction for that sentence. How do we tweak the scores? We do it using a trick called the _perceptron trick_, which we learn in the section “The perception trick.” An equivalent way to train perceptron models is to use an error function, just as we did in chapter 3. We then use gradient descent to minimize this function.

However, language is complicated—it has nuances, double entendres, and sarcasm. Wouldn’t we lose too much information if we reduce a word to a simple score? The answer is yes—we do lose a lot of information, and we won’t be able to create a perfect classifier this way. The good news is that using this method, we can still create a classifier that is correct _most_ of the time. Here is a proof that the method we are using can’t be correct all the time. The sentences, “I am not sad, I’m happy” and “I am not happy, I am sad” have the same words, yet completely different meanings. Therefore, no matter what scores we give the words, these two sentences will attain the exact same score, and, thus, the classifier will return the same prediction for them. They have different labels, so the classifier must have made a mistake with one of them.

A solution for this problem is to build a classifier that takes the order of the words into account, or even other things such as punctuation or idioms. Some models such as _hidden Markov models_ (HMM), _recurrent neural networks_ (RNN), or _long short-term memory networks_ (LSTM) have had great success with sequential data, but we won’t include them in this book. However, if you want to explore these models, in appendix C you can find some very useful references for that.

You can find all the code for this chapter in the following GitHub repository: [https://github.com/luisguiserrano/manning/tree/master/Chapter_5_Perceptron_Algorithm](https://github.com/luisguiserrano/manning/tree/master/Chapter_5_Perceptron_Algorithm).

## The problem: We are on an alien planet, and we don’t know their language!

Imagine the following scenario: we are astronauts and have just landed on a distant planet where a race of unknown aliens live. We would love to be able to communicate with the aliens, but they speak a strange language that we don’t understand. We notice that the aliens have two moods, happy and sad. Our first step in communicating with them is to figure out if they are happy or sad based on what they say. In other words, we want to build a sentiment analysis classifier.

We manage to befriend four aliens, and we start observing their mood and studying what they say. We observe that two of them are happy and two of them are sad. They also keep repeating the same sentence over and over. Their language seems to only have two words: _aack_ and _beep_. We form the following dataset with the sentence they say and their mood:

Dataset:

- Alien 1
    - Mood: Happy
    - Sentence: _“Aack, aack, aack!”_
- Alien 2:
    - Mood: Sad
    - Sentence: _“Beep beep!”_
- Alien 3:
    - Mood: Happy
    - Sentence: _“Aack beep aack!”_
- Alien 4:
    - Mood: Sad
    - Sentence: _“Aack beep beep beep!”_

All of a sudden, a fifth alien comes in, and it says, “_Aack beep aack aack_!” We can’t really tell the mood of this alien. From what we know, how should we predict for the mood of the alien (figure 5.1)?

We predict that this alien is happy because, even though we don’t know the language, the word _aack_ seems to appear more in happy sentences, whereas the word _beep_ seems to appear more in sad sentences. Perhaps _aack_ means something positive, such as “joy” or “happiness,” whereas _beep_ may mean something sad, such as “despair” or “sadness.”

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-1.png)

Figure 5.1 Our dataset of aliens. We have recorded their mood (happy or sad) and the sentence they keep repeating. Now a fifth alien comes in, saying a different sentence. Do we predict that this alien is happy or sad?

This observation gives rise to our first sentiment analysis classifier. This classifier makes a prediction in the following way: it counts the number of appearances of the words _aack_ and _beep_. If the number of appearances of _aack_ is larger than that of _beep_, then the classifier predicts that the sentence is happy. If it is smaller, then the classifier predicts that the sentence is sad. What happens when both words appear the same number of times? We have no basis to tell, so let’s say that by default, the prediction is that the sentence is happy. In practice, these types of edge cases don’t happen often, so they won’t create a big problem for us.

The classifier we just built is a perceptron (also called linear classifier). We can write it in terms of scores, or weights, in the following way:

SENTIMENT ANALYSIS CLASSIFIER

Given a sentence, assign the following scores to the words:

Scores:

- _Aack_:  1 point
- _Beep_: –1 points

Rule:

Calculate the score of the sentence by adding the scores of all the words on it as follows:

- If the score is positive or zero, predict that the sentence is happy.
- If the score is negative, predict that the sentence is sad.

In most situations, it is useful to plot our data, because sometimes nice patterns become visible. In table 5.1, we have our four aliens, as well as the number of times each said the words _aack_ and _beep_, and their mood.

Table 5.1 Our dataset of aliens, the sentences they said, and their mood. We have broken each sentence down to its number of appearances of the words aack and beep.

|   |   |   |   |
|---|---|---|---|
|Sentence|Aack|Beep|Mood|
|Aack aack aack!|3|0|Happy|
|Beep beep!|0|2|Sad|
|Aack beep aack!|2|1|Happy|
|Aack beep beep beep!|1|3|Sad|

The plot consists of two axes, the horizontal (_x_) axis and the vertical (_y_) axis. In the horizontal axis, we record the number of appearances of _aack_, and in the vertical axis, the appearances of _beep_. This plot can be seen in figure 5.2.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-2.png)

Figure 5.2 A plot of the dataset of aliens. In the horizontal axis we plot the number of appearances of the word _aack_, and in the vertical axis, the appearances of the word _beep_.

Note that in the plot in figure 5.2, the happy aliens are located on the bottom right, whereas the sad aliens are in the top left. This is because the bottom right is the area where the sentences have more appearances of _aack_ than _beep_, and the top left area is the opposite. In fact, a line formed by all the sentences with the same number of appearances of _aack_ and _beep_ divides these two regions, as shown in figure 5.3. This line has the following equation:

#aack = #beep

Or equivalently, this equation:

#aack – #beep = 0

Throughout this chapter, we’ll use the variables _x_ with different subscripts to indicate the number of appearances of a word in a sentence. In this case, _x_aack is the number of times the word _aack_ appears, and _x_beep is the number of times the word _beep_ appears.

Using this notation, the equation of the classifier becomes _x_aack – _x_beep = 0, or equivalently, _x_aack = _x_beep _._ This is the equation of a line in the plane. If it doesn’t appear so, think of the equation of the line _y_ = _x_, except instead of _x_, we have _x_aack, and instead of _y_, we have _x_beep _._ Why not use _x_ and _y_ instead like we’ve done since high school? I would love to, but unfortunately we need the _y_ for something else (the prediction) later. Thus, let’s think of the _x_aack-axis as the horizontal axis and the _x_beep-axis as the vertical axis. Together with this equation, we have two important areas, which we call the _positive zone_ and the _negative zone_. They are defined as follows:

**Positive zone**: The area on the plane for which _x_aack – _x_beep ≥ 0. This corresponds to the sentences in which the word _aack_ appears at least as many times as the word _beep_.

**Negative zone**: The area on the plane for which _x_aack – _x_beep _<_ 0. This corresponds to the sentences in which the word _aack_ appears fewer times than the word _beep_.

The classifier we created predicts that every sentence in the positive zone is happy and every sentence in the negative zone is sad. Therefore, our goal is to find the classifier that can put as many happy sentences as possible in the positive area and as many sad sentences as possible in the negative area. For this small example, our classifier achieves this job to perfection. This is not always the case, but the perceptron algorithm will help us find a classifier that will perform this job really well.

In figure 5.3, we can see the line that corresponds to the classifier and the positive and negative zones. If you compare figures 5.2 and 5.3, you can see that the current classifier is good, because all the happy sentences are in the positive zone and all the sad sentences are in the negative zone.

Now that we’ve built a simple sentiment analysis perceptron classifier, let’s look at a slightly more complex example.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-3.png)

Figure 5.3 The classifier is the diagonal line that splits the happy and the sad points. The equation of this line is _xaack_ = _xbeep_ (or equivalently, _xaack_ – _xbeep_ = 0), because the line corresponds to all the points where the horizontal and the vertical coordinates are equal. The happy zone is the zone in which the number of appearances of _aack_ is greater than or equal to the number of appearances of _beep_, and the sad zone is the zone in which the number of appearances of _aack_ is less than that of _beep_.

A slightly more complicated planet

In this section, we see a more complicated example, which introduces a new aspect of the perceptron: the bias. After we can communicate with the aliens on the first planet, we are sent on a mission to a second planet, where the aliens have a slightly more complicated language. Our goal is still the same: to create a sentiment analysis classifier in their language. The language in the new planet has two words: _crack_ and _doink_. The dataset is shown in table 5.2.

Building a classifier for this dataset seems to be a bit harder than for the previous dataset. First of all, should we assign positive or negative scores to the words _crack_ and _doink_? Let’s take a pen and paper and try coming up with a classifier that can correctly separate the happy and sad sentences in this dataset. Looking at the plot of this dataset in figure 5.4 may be helpful.

Table 5.2 The new dataset of alien words. Again, we’ve recorded each sentence, the number of appearances of each word in that sentence, and the mood of the alien.

|   |   |   |   |
|---|---|---|---|
|Sentence|Crack|Doink|Mood|
|Crack!|1|0|Sad|
|Doink doink!|0|2|Sad|
|Crack doink!|1|1|Sad|
|Crack doink crack!|2|1|Sad|
|Doink crack doink doink!|1|3|Happy|
|Crack doink doink crack!|2|2|Happy|
|Doink doink crack crack crack!|3|2|Happy|
|Crack doink doink crack doink!|2|3|Happy|

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-4.png)

Figure 5.4 The plot of the new dataset of aliens. Notice that the happy ones tend to be above and to the right, and the sad ones below and to the left.

The idea for this classifier is to count the number of words in a sentence. Notice that the sentences with one, two, or three words are all sad, and the sentences with four and five words are happy. That is the classifier! It classifies sentences with three words or fewer as sad, and the sentences with four words or more as happy. We can again write this in a more mathematical way.

SENTIMENT ANALYSIS CLASSIFIER

Given a sentence, assign the following scores to the words:

Scores:

- _Crack_: one point
- _Doink_: one point

Rule:

Calculate the score of the sentence by adding the scores of all the words on it.

- If the score is four or more, predict that the sentence is happy.
- If the score is three or less, predict that the sentence is sad.

To make it simpler, let’s slightly change the rule by using a cutoff of 3.5.

Rule:

Calculate the score of the sentence by adding the scores of all the words on it.

- If the score is 3.5 or more, predict that the sentence is happy.
- If the score is less than 3.5, predict that the sentence is sad.

This classifier again corresponds to a line, and that line is illustrated in figure 5.5.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-5.png)

Figure 5.5 The classifier for the new dataset of aliens. It is again a line that splits the happy and the sad aliens.

In the previous example, we concluded that the word _aack_ was a happy word, and the word _beep_ was a sad one. What happens in this example? It seems that both words _crack_ and _doink_ are happy, because their scores are both positive. Why, then, is the sentence “_Crack doink_” a sad sentence? It doesn’t have enough words. The aliens on this planet have a distinctive personality. The aliens that don’t speak much are sad, and those who speak a lot are happy. The way we can interpret it is that the aliens on this planet are inherently sad, but they can get out of the sadness by talking a lot.

Another important element in this classifier is the cutoff, or threshold, of 3.5. This threshold is used by the classifier to make the prediction, because sentences with scores higher than or equal to the threshold are classified as happy, and sentences with scores lower than the threshold are classified as sad. However, thresholds are not common, and instead we use the notion of a _bias_. The bias is the negative of the threshold, and we add it to the score. This way, the classifier can calculate the score and return a prediction of happy if the score is nonnegative, or sad if it is negative. As a final change in notation, we’ll call the scores of the words _weights_. Our classifier can be expressed as follows:

SENTIMENT ANALYSIS CLASSIFIER

Given a sentence, assign the following weights and bias to the words:

Weights:

- _Crack_: one point
- _Doink_: one point

**Bias**: –3.5 points

Rule:

Calculate the score of the sentence by adding the weights of all the words on it and the bias.

- If the score is greater than or equal to zero, predict that the sentence is happy.
- If the score is less than zero, predict that the sentence is sad.

The equation of the score of the classifier, and also of the line in figure 5.5, follows:

#crack + #doink – 3.5 = 0

Notice that defining a perceptron classifier with a threshold of 3.5 and with a bias of –3.5 is the same thing, because the following two equations are equivalent:

- #crack + #doink ≥ 3.5
- #crack + #doink – 3.5 ≥ 0

We can use a similar notation as in the previous section, where _x_crack is the number of appearances of the word _crack_ and _x_doink is the number of appearances of the word _doink_. Thus, the equation of the line in figure 3.5 can be written as

_x_crack + _x_doink – 3.5 = 0.

This line also divides the plane into positive and negative zones, defined as follows:

**Positive zone**: the area on the plane for which _x_crack + _x_doink – 3.5 ≥ 0

**Negative zone**: the area on the plane for which _x_crack + _x_doink – 3.5 < 0

Does our classifier need to be correct all the time? No

In the previous two examples, we built a classifier that was correct all the time. In other words, the classifier classified the two happy sentences as happy and the two sad sentences as sad. This is not something one finds often in practice, especially in datasets with many points. However, the goal of the classifier is to classify the points as best as possible. In figure 5.6, we can see a dataset with 17 points (eight happy and nine sad) that is impossible to perfectly split into two using a single line. However, the line in the picture does a good job, only classifying three points incorrectly.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-6.png)

Figure 5.6 This line splits the dataset well. Note that it makes only three mistakes: two on the happy zone and one on the sad zone.

A more general classifier and a slightly different way to define lines

In this section, we get a more general view of the perceptron classifier. For a moment, let’s call our words 1 and 2, and the variables keeping track of their appearances _x_1 and _x_2. The equations of the two previous classifiers follow:

- _x_1 – _x_2 = 0
- _x_1 + _x_2 – 3.5 = 0

The general form of the equation of a perceptron classifier is _ax_1 + _bx_2 + _c_ = 0, where _a_ is the score of the word 1, _b_ the score of the word 2, and _c_ is the bias. This equation corresponds to a line that splits the plane into two zones as follows:

**Positive zone**: the zone on the plane for which _ax_1 + _bx_2 + _c_ ≥ 0

**Negative zone**: the zone on the plane for which _ax_1 + _bx_2 + _c_ < 0

For example, if the word 1 has a score of 4, the word 2 has a score of –2.5, and the bias is 1.8, then the equation of this classifier is

4_x_1 – 2.5_x_2 + 1.8 = 0,

and the positive and negative zones are those where 4_x_1 – 2.5_x_2 + 1.8 ≥ 0 and 4_x_1 – 2.5_x_2 + 1.8 < 0, respectively.

aside: Equations of lines and zones in the plane In chapter 3, we defined lines using the equation _y_ = _mx_ + _b_ on a plane where the axes are _x_ and _y_. In this chapter, we define them with the equation _ax_1 + _bx_2 + _c_ = 0 on a plane where the axes are _x_1 and _x_2. How are they different? They are both perfectly valid ways to define a line. However, whereas the first equation is useful for linear regression models, the second equation is useful for perceptron models (and, in general, for other classification algorithms, such as logistic regression, neural networks, and support vector machines, that we’ll see in chapters 6, 10, and 11, respectively). Why is this equation better for perceptron models? Some advantages follow:

- The equation _ax_1 + _bx_2 + _c_ = 0 not only defines a line but also clearly defines the two zones, positive and negative. If we wanted to have the same line, except with the positive and negative regions flipped, we would consider the equation –_ax_1 – _bx_2 – _c_ = 0.
- Using the equation _ax_1 + _bx_2 + _c_ = 0, we can draw vertical lines, because the equation of a vertical line is _x_ = _c_ or 1_x_1 + 0_x_2 – _c_ = 0. Although vertical lines don’t often show up in linear regression models, they do show up in classification models.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-7.png)

Figure 5.7 A classifier is defined by a line with the equation _ax_1 + _bx_2 + _c_ = 0, a positive zone, and a negative zone. If we want to flip the positive and negative zones, all we need to do is negate the weights and the bias. On the left we have the classifier with equation _ax_1 + _bx_2 + _c_ = 0. On the right, the classifier with flipped zones and the equation –_ax_1 – _bx_2 – _c_ = 0.

The step function and activation functions: A condensed way to get predictions

In this section, we learn a mathematical shortcut to obtain the predictions. Before learning this, however, we need to turn all our data into numbers. Notice that the labels in our dataset are “happy” and “sad.” We record these as 1 and 0, respectively.

Both perceptron classifiers that we’ve built in this chapter have been defined using an if statement. Namely, the classifier predicts “happy” or “sad” based on the total score of the sentence; if this score is positive or zero, the classifier predicts “happy,” and if it is negative, the classifier predicts “sad.” We have a more direct way to turn the score into a prediction: using the _step function_.

step function The function that returns a 1 if the output is nonnegative and a 0 if the output is negative. In other words, if the input is _x_, then

- _step_(_x_) = 1 if _x_ ≥ 0
- _step_(_x_) = 0 if _x_ < 0

Figure 5.8 shows the graph of the step function.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-8.png)

Figure 5.8 The step function is useful in the study of perceptron models. The output of the step function is 0 when the input is negative and 1 otherwise.

With the step function, we can express the output of the perceptron classifier easily. In our dataset, we use the variable _y_ to refer to the labels, just as we did in chapter 3. The prediction that the model makes for the label is denoted _y__ˆ._ The output of the perceptron model is written in condensed form as

_ŷ_ = _step_(_ax_1 + _bx_2 + _c_).

The step function is a specific case of an _activation function_. The activation function is an important concept in machine learning, especially in deep learning and will appear again in chapters 6 and 10. The formal definition of an activation function will come later, because its full power is used in building neural networks. But for now, we can think of the activation function as a function we can use to turn the scores into a prediction.

What happens if I have more than two words? General definition of the perceptron classifier

In the two alien examples at the beginning of this section, we built perceptron classifiers for languages with two words. But we can build classifiers with as many words as we want. For example, if we had a language with three words, say, _aack_, _beep_, and _crack_, the classifier would make predictions according to the following formula:

_ŷ_ = _step_(_ax_aack + _bx_beep + _cx_crack + _d_),

where _a_, _b_, and _c_ are the weights of the words _aack_, _beep_, and _crack_, respectively, and _d_ is the bias.

As we saw, the sentiment analysis perceptron classifiers for languages with two words can be expressed as a line in the plane that splits the happy and the sad points. Sentiment analysis classifiers for languages with three words can also be represented geometrically. We can imagine the points as living in three-dimensional space. In this case, each of the axes corresponds to each of the words _aack_, _beep_, and _crack_, and a sentence corresponds to a point in space for which its three coordinates are the number of appearances of the three words. Figure 5.9 illustrates an example in which the sentence containing _aack_ five times, _beep_ eight times, and _crack_ three times, corresponds to the point with coordinates (5, 8, 3).

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-9.png)

Figure 5.9 A sentence with three words can be plotted as a point in space. In this case, a sentence with the word aack five times, beep eight times, and crack three times is plotted in the point with coordinates (5,8,3).

The way to separate these points is using a plane. The equation with a plane is precisely _ax_aack + _bx_beep + _cx_crack + _d_, and this plane is illustrated in figure 5.10.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-10.png)

Figure 5.10 A dataset of sentences with three words is plotted in three dimensions. The classifier is represented by a plane that splits the space into two regions.

We can build sentiment analysis perceptron classifiers for languages with as many words as we can. Say our language has _n_ words, that we call 1, 2, … , _n_. Our dataset consists of _m_ sentences, which we call _x_(1), _x_(2), … , _x_(m). Each sentence _x_(1) comes with a label _y_i, which is 1 if the sentence is happy and 0 if it is sad. The way we record each sentence is using the number of appearances of each of the _n_ words. Therefore, each sentence corresponds to a row in our dataset and can be seen as a vector, or an _n_-tuple of numbers _x_(i) = (_x_1(i), _x_2(i), … , _x_n(i)), where _x_j(i) is the number of appearances of the word _j_ in the _i_-th sentence.

The perceptron classifier consists of _n_ weights (scores), one for each of the _n_ words in our language, and a bias. The weights are denoted _w_i and the bias _b_. Thus, the prediction that the classifier makes for the sentence _x_(i) is

_ŷ_i = _step_(_w_1_x_1(i) + _w_2_x_2(i) + … +_w_n_x_n(i) + _b_).

In the same way as the classifiers with two words can be represented geometrically as a line that cuts the plane into two regions, and the classifiers with three words can be represented as a plane that cuts the three-dimensional space into two regions, classifiers with _n_ words can also be represented geometrically. Unfortunately, we need _n_-dimensions to see them. Humans can see only three dimensions, so we may have to imagine an (_n_-1)-dimensional plane (called a hyperplane) cutting the _n_-dimensional space into two regions.

However, the fact that we can’t imagine them geometrically doesn’t mean we can’t have a good idea of how they work. Imagine if our classifier is built on the English language. Every single word gets a weight assigned. That is equivalent to going through the dictionary and assigning a happiness score to each of the words. The result could look something like this:

Weights (scores):

- A: 0.1 points
- Aardvark: 0.2 points
- Aargh: –4 points
- …
- Joy: 10 points
- …
- Suffering: –8.5 points
- ...
- Zygote: 0.4 points

Bias:

- –2.3 points

If those were the weights and bias of the classifier, to predict whether a sentence is happy or sad, we add the scores of all the words on it (with repetitions). If the result is higher than or equal to 2.3 (the negative of the bias), the sentence is predicted as happy; otherwise, it is predicted as sad.

Furthermore, this notation works for any example, not only sentiment analysis. If we have a different problem with different data points, features, and labels, we can encode it using the same variables. For example, if we have a medical application where we are trying to predict whether a patient is sick or healthy using _n_ weights and a bias, we can still call the labels _y_, the features _x_i, the weights _w_i, and the bias _b_.

The bias, the _y_-intercept, and the inherent mood of a quiet alien

So far we have a good idea of what the weights of the classifier mean. Words with positive weights are happy, and words with negative words are sad. Words with very small weights (whether positive or negative) are more neutral words. However, what does the bias mean?

In chapter 3, we specified that the bias in a regression model for house prices was the base price of a house. In other words, it is the predicted price of a hypothetical house with zero rooms (a studio?). In the perceptron model, the bias can be interpreted as the score of the empty sentence. In other words, if an alien says absolutely nothing, is this alien happy or sad? If a sentence has no words, its score is precisely the bias. Thus, if the bias is positive, the alien that says nothing is happy, and if the bias is negative, that same alien is sad.

Geometrically, the difference between a positive and negative bias lies in the location of the origin (the point with coordinates (0,0)) with respect to the classifier. This is because the point with coordinates (0,0) corresponds to the sentence with no words. In classifiers with a positive bias, the origin lies in the positive zone, whereas in classifiers with a negative bias, the origin lies in the negative zone, as illustrated in figure 5.11.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-11.png)

Figure 5.11 Left: the classifier has a negative bias, or a positive _threshold_ (_y_-intercept). This means that the alien that doesn’t say anything falls in the sad zone and is classified as sad. Right: The classifier has a positive bias, or a negative threshold. This means that the alien that doesn’t say anything falls in the happy zone and is classified as happy.

Can we think of sentiment analysis datasets in which the bias is positive or negative? What about the following two examples:

**Example 1 (positive bias)**: a dataset of online reviews of a product

Imagine a dataset in which we record all the reviews of a particular product on Amazon. Some of them are positive, and some of them are negative, according to the number of stars they receive. What do you think the score would be for an empty review? From my experience, bad reviews tend to contain lots of words, because the customer is upset, and they describe their negative experience. However, many of the positive reviews are empty—the customer simply gives a good score, without the need to explain why they enjoyed the product. Therefore, this classifier probably has a positive bias.

**Example 2 (negative bias)**: a dataset of conversations with friends

Imagine that we record all our conversations with friends and classify them as happy or sad conversations. If one day we bump into a friend, and our friend says absolutely nothing, we imagine that they are mad at us or that they are very upset. Therefore, the empty sentence is classified as sad. This means that this classifier probably has a negative bias.

## How do we determine whether a classifier is good or bad? The error function

Now that we have defined what a perceptron classifier is, our next goal is to understand how to train it—in other words, how do we find the perceptron classifier that best fits our data? But before learning how to train perceptrons, we need to learn an important concept: how to evaluate them. More specifically, in this section we learn a useful error function that will tell us whether a perceptron classifier fits our data well. In the same way that the absolute and square errors worked for linear regression in chapter 3, this new error function will be large for classifiers that don’t fit the data well and small for those that fit the data well.

How to compare classifiers? The error function

In this section, we learn how to build an effective error function that helps us determine how good a particular perceptron classifier is. First, let’s test our intuition. Figure 5.12 shows two different perceptron classifiers on the same dataset. The classifiers are represented as a line with two well-defined sides, happy and sad. Clearly, the one on the left is a bad classifier, and the one on the right is good. Can we come up with a measure of how good they are? In other words, can we assign a number to each one of them, in a way that the one on the left is assigned a high number and the one on the right a low number?

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-12.png)

Figure 5.12 Left: a bad classifier, which doesn’t really split the points well. Right: a good classifier. Can we think of an error function that assigns a high number to the bad classifier and a low number to the good one?

Next, we see different answers to this question, all with some pros and cons. One of them (spoiler: the third one) is the one we use to train perceptrons.

ERROR FUNCTION 1: NUMBER OF ERRORS

The simplest way to evaluate a classifier is by counting the number of mistakes it makes—in other words, by counting the number of points that it classifies incorrectly.

In this case, the classifier on the left has an error of 8, because it erroneously predicts four happy points as sad, and four sad points as happy. The good classifier has an error of 3, because it erroneously predicts one happy point as sad, and two sad points as happy. This is illustrated in figure 5.13.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-13.png)

Figure 5.13 We evaluate the two classifiers by counting the number of points that each one of them misclassifies. The classifier on the left misclassifies eight points, whereas the classifier on the right misclassifies three points. Thus, we conclude that the classifier on the right is a better one for our dataset.

This is a good error function, but it’s not a great error function. Why? It tells us when there is an error, but it doesn’t measure the gravity of the error. For example, if a sentence is sad, and the classifier gave it a score of 1, the classifier made a mistake. However, if another classifier gave it a score of 100, this classifier made a much bigger mistake. The way to see this geometrically is in figure 5.14. In this image, both classifiers misclassified a sad point by predicting that it is happy. However, the classifier on the left located the line close to the point, which means that the sad point is not too far from the sad zone. The classifier on the right, in contrast, has located the point very far from its sad zone.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-14.png)

Figure 5.14 The two classifiers misclassify the point. However, the classifier on the right made a much bigger mistake than the classifier on the left. The point on the left is not far from the boundary, and thus, it is not very far from the sad zone. However, the point on the right is very far from the sad zone. Ideally, we would like an error function that assigns a higher error to the classifier in the right than to the classifier in the left.

Why do we care about measuring how bad an error is? Wouldn’t it be enough to count them? Recall what we did in chapter 3 with the linear regression algorithm. More specifically, recall the section “Gradient descent,” where we used gradient descent to reduce this error. The way to reduce an error is by decreasing it in small amounts, until we reach a point where the error is small. In the linear regression algorithm, we wiggled the line small amounts and picked the direction in which the error decreased the most. If our error is calculated by counting the number of misclassified points, then this error will take only integer values. If we wiggle the line a small amount, the error may not decrease at all, and we don’t know in which direction to move. The goal of gradient descent is to minimize a function by taking small steps in the direction in which the function decreases the most. If the function takes only integer values, this is equivalent to trying to descend from an Aztec staircase. When we are at a flat step, we don’t know what step to take, because the function doesn’t decrease in any direction. This is illustrated in figure 5.15.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-15.png)

Figure 5.15 Performing gradient descent to minimize an error function is like descending from a mountain by taking small steps. However, for us to do that, the error function must not be flat (like the one on the right), because in a flat error function, taking a small step will not decrease the error. A good error function is like the one on the left, in which we can easily see the direction we must use to take a step to slightly decrease the error function.

We need a function that measures the magnitude of an error and that assigns a higher error to misclassified points that are far from the boundary than to those that are close to it.

ERROR FUNCTION 2: DISTANCE

A way to tell the two classifiers apart in figure 5.16 is by considering the perpendicular distance from the point to the line. Notice that for the classifier on the left, this distance is small, whereas for the classifier on the right, the distance is large.

This error function is much more effective. What this error function does follows:

- Points that are correctly classified produce an error of 0.
- Points that are misclassified produce an error equal to the distance from that point to the line.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-16.png)

Figure 5.16 An effective way to measure how bad a classifier misclassifies a point is by measuring the perpendicular distance from the point to the line. For the classifier on the left, this distance is small, whereas for the classifier on the right, the distance is large.

Let’s go back to the two classifiers we had at the beginning of this section. The way we calculate the total error is by adding the errors corresponding to all the data points, as illustrated in figure 5.17. This means that we look only at the misclassified points and add the perpendicular distances from these points to the line. Notice that the bad classifier has a large error, and the good classifier has a small error.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-17.png)

Figure 5.17 To calculate the total error of a classifier, we add up all the errors, which are the perpendicular distances from the misclassified points. The error is large for the classifier on the left and small for the classifier on the right. Thus, we conclude that the classifier on the right is better.

This is _almost_ the error function we will use. Why don’t we use this one? Because the distance from a point to a line is a complicated formula. It contains a square root, because we calculate it using the Pythagorean theorem. Square roots have complicated derivatives, which adds unnecessary complexity the moment we apply the gradient descent algorithm. We don’t need to undertake this complication, because we can instead create an error function that is easier to calculate yet still manages to capture the essence of an error function: returning an error for points that are misclassified and varying the magnitude based on how far the misclassified point is from the boundary.

ERROR FUNCTION 3: SCORE

In this section, we see how to build the standard error function for perceptrons, which we call the _perceptron error function_. First, let’s summarize the properties we want in an error function as follows:

- The error function of a correctly classified point is 0.
- The error function of an incorrectly classified point is a positive number.
    - For misclassified points close to the boundary, the error function is small.
    - For misclassified points far from the boundary, the error function is large.
- It is given by a simple formula.

Recall that the classifier predicts a label of 1 for points in the positive zone and a label of 0 for points in the negative zone. Therefore, a misclassified point is either a point with label 0 in the positive zone, or a point with label 1 in the negative zone.

To build the perceptron error function, we use the score. In particular, we use the following properties of the score:

Properties of the score:

1. The points in the boundary have a score of 0.
2. The points in the positive zone have positive scores.
3. The points in the negative zone have negative scores.
4. The points close to the boundary have scores of low magnitude (i.e., positive or negative scores of low absolute value).
5. The points far from the boundary have scores of high magnitude (i.e., positive or negative scores of high absolute value).

For a misclassified point, the perceptron error wants to assign a value that is proportional to its distance to the boundary. Therefore, the error for misclassified points that are far from the boundary must be high, and the error for misclassified points that are close to the boundary must be low. Looking at properties 4 and 5, we can see that the absolute value of the score is always high for points far from the boundary and low for points close to the boundary. Thus, we define the error as the absolute value of the score for misclassified points.

More specifically, consider the classifier that assigns weights of _a_ and _b_ to the words _aack_ and _beep_, and has a bias of _c_. This classifier makes the prediction _ŷ_ = _step_(_ax_aack + _bx_beep + _c_) to the sentence with _x_aack appearances of the word _aack_ and _x_beep appearances of the word _beep_. The perceptron error is defined as follows:

PERCEPTRON ERROR FOR A SENTENCE

- If the sentence is correctly classified, the error is 0.
- If the sentence is misclassified, the error is |_x_aack + _bx_beep + _c_|.

In the general scenario, where the notation is defined as in the section “What happens if I have more than two words?,” the following is the definition of the perceptron error:

PERCEPTRON ERROR FOR A POINT (GENERAL)

- If the point is correctly classified, the error is 0.
- If the point is misclassified, the error is |_w_1 _x_1 +_w_2 _x_2 + … +_w_n_x_n + _b_|.

THE MEAN PERCEPTRON ERROR: A WAY TO CALCULATE THE ERROR OF AN ENTIRE DATASET

To calculate the perceptron error for an entire dataset, we take the average of all the errors corresponding to all the points. We can also take the sum if we choose, although in this chapter we choose the average and call it the _mean perceptron error_.

To illustrate the mean perceptron error, let’s look at an example.

EXAMPLE

Consider the dataset made of four sentences, two labeled happy and two labeled sad, illustrated in table 5.3.

Table 5.3 The new dataset of aliens. Again, we’ve recorded each sentence, the number of appearances of each word in that sentence, and the mood of the alien.

|   |   |   |   |
|---|---|---|---|
|Sentence|Aack|Beep|Label (mood)|
|Aack|1|0|Sad|
|Beep|0|1|Happy|
|Aack beep beep beep|1|3|Happy|
|Aack beep beep aack aack|3|2|Sad|

We’ll compare the following two classifiers on this dataset:

CLASSIFIER 1

Weights:

- _Aack_: _a_ = 1

- _Beep_: _b_ = 2

**Bias**: _c_ = –4

**Score of a sentence**: 1_x_aack + 2_x_beep – 4

**Prediction**: _ŷ_ = _step_(1_x_aack + 2_x_beep – 4)

CLASSIFIER 2

Weights:

- _Aack_: _a_ = –1
- _Beep_: _b_ = 1

**Bias**: _c_ = 0

**Score of a sentence**: –_x_aack + _x_beep

**Prediction**: _ŷ_ = _step_(–_x_aack + _x_beep)

The points and the classifiers can be seen in figure 5.18. At first glance, which one looks like a better classifier? It appears classifier 2 is better, because it classifies every point correctly, whereas classifier 1 makes two mistakes. Now let’s calculate the errors and make sure that classifier 1 has a higher error than classifier 2.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-18.png)

Figure 5.18 On the left we have classifier 1, and on the right we have classifier 2.

The predictions for both classifiers are calculated in table 5.4.

Table 5.4 Our dataset of four sentences with their labels. For each of the two classifiers, we have the score and the prediction.

|   |   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|---|
|Sentence ( _x_aack, _x_beep)|Label<br><br>_y_|Classifier 1 score<br><br>1_x_aack + 2_x_beep – 4|Classifier 1 prediction<br><br>_step_(1_x_aack + 2_x_beep – 4)|Classifier 1 error|Classifier 2 score<br><br>– _x_aack + 2_x_beep|Classifier 2 prediction<br><br>_step_(–_x_aack + 2 _x_beep)|Classifier 2 error|
|(1,0)|Sad (0)|–3|0 (correct)|0|–1|0 (correct)|0|
|(0,1)|Happy (1)|-2|0 (incorrect)|2|1|1 (correct)|0|
|(1,3)|Happy (1)|3|1 (correct)|3|2|1 (correct)|0|
|(3,2)|Sad (0)|3|1 (incorrect)|0|–1|0 (correct)|0|
|Mean perceptron error||   |   |1.25||   |0|

Now on to calculate the errors. Note that classifier 1 misclassified only sentences 2 and 4. Sentence 2 is happy, but it is misclassified as sad, and sentence 4 is sad, but it is misclassified as happy. The error of sentence 2 is the absolute value of the score, or |–2| = 2. The error of sentence 4 is the absolute value of the score, or |3| = 3. The other two sentences have an error of 0, because they are correctly classified. Thus, the mean perceptron error of classifier 1 is

1/4(0 + 2 + 0 + 3) = 1.25.

Classifier 2 makes no errors—it correctly classifies all the points. Therefore, the mean perceptron error of classifier 2 is 0. We then conclude that classifier 2 is better than classifier 1. The summary of these calculations is shown in table 5.4 and figure 5.19.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-191.png)

Figure 5.19 Classifier 1 has an error of 1.25, whereas classifier 2 has an error of 0. Thus, we conclude that classifier 2 is better than classifier 1.

Now that we know how to compare classifiers, let’s move on to finding the best one of them, or at least a pretty good one.

## How to find a good classifier? The perceptron algorithm

To build a good perceptron classifier, we’ll follow a similar approach as the one we followed with linear regression in chapter 3. The process is called the _perceptron algorithm_, and it consists of starting with a random perceptron classifier and slowly improving it until we have a good one. The main steps of the perceptron algorithm follow:

1. Start with a random perceptron classifier.
2. Slightly improve the classifier. (Repeat many times).
3. Measure the perceptron error to decide when to stop running the loop.

We start by developing the step inside the loop, a technique used to slightly improve a perceptron classifier called _the perceptron trick_. It is similar to the square and absolute tricks we learned in the sections “The square trick” and “The absolute trick” in chapter 3.

The perceptron trick: A way to slightly improve the perceptron

The perceptron trick is a tiny step that helps us go from a perceptron classifier to a slightly better perceptron classifier. However, we’ll start by describing a slightly less ambitious step. Just as we did in chapter 3, we’ll first focus on one point and try to improve the classifier for that one point.

There are two ways to see the perceptron step, although both are equivalent. The first way is the geometric way, where we think of the classifier as a line.

PSEUDOCODE FOR THE PERCEPTRON TRICK (GEOMETRIC)

- **Case 1**: If the point is correctly classified, leave the line as it is.
- **Case 2**: If the point is incorrectly classified, move the line a little closer to the point.

Why does this work? Let’s think about it. If the point is misclassified, it means it is on the wrong side of the line. Moving the line closer to it may not put it on the right side, but at least it gets it closer to the line and, thus, closer to the correct side of the line. We repeat this process many times, so it is imaginable that one day we’ll be able to move the line past the point, thus correctly classifying it. This process is illustrated in figure 5.20.

We also have an algebraic way to see the perceptron trick.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-20.png)

Figure 5.20 Case 1 (left): A point that is correctly classified tells the line to stay where it is. Case 2 (right): A point that is misclassified tells the line to move closer toward it.

PSEUDOCODE FOR THE PERCEPTRON TRICK (ALGEBRAIC)

- **Case 1**: If the point is correctly classified, leave the classifier as it is.
- **Case 2**: If the point is incorrectly classified, that means it produces a positive error. Adjust the weights and the bias a small amount so that this error slightly decreases.

The geometric way is an easier way to visualize this trick, but the algebraic way is an easier way to develop it, so we’ll look at it the algebraic way. First, let’s use our intuition. Imagine that we have a classifier for the entire English language. We try this classifier on the sentence “I am sad,” and it predicts that the sentence is happy. This is clearly wrong. Where could we have gone wrong? If the prediction is that the sentence is happy, then the sentence must have received a positive score. This sentence shouldn’t receive a positive score—it should receive a negative score to be classified as sad. The score is calculated as the sum of the scores of its words _I_, _am_, and _sad_, plus the bias. We need to decrease this score, to make the sentence slightly sadder. It is OK if we decrease it only a little bit, and the score is still positive. Our hope is that running this process many times will one day turn the score into negative and correctly classify our sentence. The way to decrease the score is by decreasing all its parts, namely, the weights of the words _I_, _am_, and _sad_ and the bias. By how much should we decrease them? We decrease them by an amount equal to the learning rate that we learned in the section “The square trick” in chapter 3.

Similarly, if our classifier misclassifies the sentence “I am happy” as a sad sentence, then our procedure is to slightly increase the weights of the words _I_, _am_, and _happy_ and the bias by an amount equal to the learning rate.

Let’s illustrate this with a numerical example. In this example, we use a learning rate of _η_ = 0.01. Imagine that we have the same classifier that we had in the previous section, namely, the one with the following weights and bias. We’ll call it the bad classifier, because our goal is to improve it.

BAD CLASSIFIER

Weights:

- _Aack_: _a_ = 1
- _Beep_: _b_ = 2

**Bias**: _c_ = –4

**Prediction**: _ŷ_ = _step_(_x_aack + 2_x_beep _–_ 4)

The following sentence is misclassified by the model, and we’ll use it to improve the weights:

**Sentence 1**: “Beep aack aack beep beep beep beep.”

**Label**: Sad (0)

For this sentence, the number of appearances of _aack_ is _x_aack = 2, and the number of appearances of _beep_ is _x_beep = 5. Thus, the score is 1 · _x_aack + 2 · _x_beep – 4 = 1 · 2 + 2 · 5 – 4 = 8, and the prediction is _ŷ_ = _step_(8) = 1.

The sentence should have had a negative score, to be classified as sad. However, the classifier gave it a score of 8, which is positive. We need to decrease this score. One way to decrease it is to subtract the learning rate to the weight of _aack_ to the weight of _beep_ and to the bias, thus obtaining new weights, which we call _a__'_ = 0.99, _b__'_ = 1.99, and a new bias _c__'_ = 4.01. However, think about this: the word _beep_ appeared many more times than the word _aack_. In some way, _beep_ is more crucial to the score of the sentence than _aack_. We should probably decrease the weight of _beep_ more than the score of _aack_. Let’s decrease the weight of each word by the learning rate times the number of times the word appears in the sentence. In other words:

- The word aack appears twice, so we’ll reduce its weight by two times the learning rate, or 0.02. We obtain a new weight _a__'_ = 1 – 2 · 0.01 = 0.98.
- The word _beep_ appears five times, so we’ll reduce its weight by five times the learning rate, or 0.05. We obtain a new weight _b__'_ = 2 – 5 · 0.01 = 1.95.
- The bias adds to the score only once, so we reduce the bias by the learning rate, or 0.01. We obtain a new bias _c__'_ = –4 – 0.01 = –4.01.

aside Instead of subtracting the learning rate from each weight, we subtracted the learning rate times the number of appearances of the word in the sentence. The true reason for this is calculus. In other words, when we develop the gradient descent method, the derivative of the error function forces us to do this. This process is detailed in appendix B, section “Using gradient descent to train classification models.”

The new improved classifier follows:

IMPROVED CLASSIFIER 1

Weights:

- _Aack_: _a__'_ = 0.98
- _Beep_: _b__'_ = 1.95

**Bias**: _c__'_ = –4.01

**Prediction**: _ŷ_ = _step_(0.98_x_aack + 1.95_x_beep – 4.01)

Let’s verify the errors of both classifiers. Recall that the error is the absolute value of the score. Thus, the bad classifier produces an error of |1 · _x_aack + 2 · _x_beep – 4| = |1 · 2 + 2 · 5 – 4| = 8. The improved classifier produces an error of |0.98 · _x_aack + 1.95 · _x_beep – 4.01| = |0.98 · 2 + 1.95 · 5 – 4.01| = 7.7. That is a smaller error, so we have indeed improved the classifier for that point!

The case we just developed consists of a misclassified point with a negative label. What happens if the misclassified point has a positive label? The procedure is the same, except instead of subtracting an amount from the weights, we add it. Let’s go back to the bad classifier and consider the following sentence:

**Sentence 2**: “Aack aack.”

**Label**: Happy

The prediction for this sentence is _ŷ_ = _step_(_x_aack + 2_x_beep – 4) = _step_(2 + 2 · 0 – 4) = _step_(–2) = 0. Because the prediction is sad, the sentence is misclassified. The score of this sentence is –2, and to classify this sentence as happy, we need the classifier to give it a positive score. The perceptron trick will increase this score of –2 by increasing the weights of the words and the bias as follows:

- The word _aack_ appears twice, so we’ll increase its weight by two times the learning rate, or 0.02. We obtain a new weight _a'_ = 1 + 2 · 0.01 = 1.02.
- The word _beep_ appears zero times, so we won’t increase its weight, because this word is irrelevant to the sentence.
- The bias adds to the score only once, so we increase the bias by the learning rate, or 0.01. We obtain a new bias _c__'_ = –4 + 0.01 = –3.99.

Thus, our new improved classifier follows:

IMPROVED CLASSIFIER 2

Weights:

- _Aack_: _a__'_ = 1.02
- _Beep_: _b__'_ = 2

**Bias**: _c__'_ = –3.99

**Prediction**: _ŷ_ = _step_(1.02_x_aack + 2_x_beep – 3.99)

Now let’s verify the errors. Because the bad classifier gave the sentence a score of –2, then the error is |–2| = 2. The second classifier gave the sentence a score of 1.02_x_aack + 2_x_beep – 3.99 = 1.02 · 2 + 2 · 0 – 3.99 = –1.95, and an error of 1.95. Thus, the improved classifier has a smaller error on that point than the bad classifier, which is exactly what we were expecting.

Let’s summarize these two cases and obtain the pseudocode for the perceptron trick.

PSEUDOCODE FOR THE PERCEPTRON TRICK

Inputs:

- A perceptron with weights _a, b,_ and bias _c_
- A point with coordinates (_x_1, _x_2) and label _y_
- A small positive value _η_ (the learning rate)

Output:

- A perceptron with new weights _a'_, _b'_, and bias _c'_

Procedure:

- The prediction the perceptron makes at the point is _ŷ_ = _step_(_ax_1 + _bx_2 + _c_).
- **Case 1**: If _ŷ = y_:
    - **Return** the original perceptron with weights _a'_, _b'_, and bias _c'_.
- **Case 2**: If _ŷ_ = 1 and _y_ = 0:
    - **Return** the perceptron with the following weights and bias:
        - _a' = a – η__x_1
        - _b' = b – η__x_2
        - _c' = c – η__x_1
- **Case 3**: If _ŷ_ = 0 and _y_ = 1:
    - **Return** the perceptron with the following weights and bias:
        - _a' = a + η__x_1
        - _b' = b – η__x_2
        - _c' = c + η__x_1

If the perceptron correctly classifies the point, the output perceptron is the same as the input, and both of them produce an error of 0. If the perceptron misclassifies the point, the output perceptron produces a smaller error than the input perceptron.

The following is a slick trick to condense the pseudocode. Note that the quantity _y_ – _ŷ_ is 0, –1, and +1 for the three cases in the perceptron trick. Thus, we can summarize it as follows:

PSEUDOCODE FOR THE PERCEPTRON TRICK

Inputs:

- A perceptron with weights _a_, _b_, and bias _c_
- A point with coordinates (_x_1, _x_2) and label _y_
- A small value _η_ (the learning rate)

Output:

- A perceptron with new weights _a'_, _b'_, and bias _c'_

Procedure:

- The prediction the perceptron makes at the point is _ŷ_ = _step_(_ax_1 + _bx_2 + _c_).
- **Return** the perceptron with the following weights and bias:
    - _a' = a_ + _η_(_y_ - _ŷ_)_x_1
    - _b' = b_ + _η_(_y_ - _ŷ_)_x_2
    - _c' = c_ + _η_(_y_ - _ŷ_)

Repeating the perceptron trick many times: The perceptron algorithm

In this section, we learn the _perceptron algorithm_, which is used to train a perceptron classifier on a dataset. Recall that the perceptron trick allows us to slightly improve a perceptron to make a better prediction on one point. The perceptron algorithm consists of starting with a random classifier and continuously improving it, using the perceptron trick many times.

As we’ve seen in this chapter, we can study this problem in two ways: geometrically and algebraically. Geometrically, the dataset is given by points in the plane colored with two colors, and the classifier is a line that tries to split these points. Figure 5.21 contains a dataset of happy and sad sentences just like the ones we saw at the beginning of this chapter. The first step of the algorithm is to draw a random line. It’s clear that the line in figure 5.21 does not represent a great perceptron classifier, because it doesn’t do a good job of splitting the happy and the sad sentences.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-21.png)

Figure 5.21 Each point tells the classifier what to do to make life better for itself. The points that are correctly classified tell the line to stay still. The points that are misclassified tell the line to move slightly toward them.

The next step in the perceptron algorithm consists of picking one point at random, such as the one in figure 5.22. If the point is correctly classified, the line is left alone. If it is misclassified, then the line gets moved slightly closer to the point, thus making the line a better fit for that point. It may become a worse fit for other points, but that doesn’t matter for now.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-22.png)

Figure 5.22 If we apply the perceptron trick to a classifier and a misclassified point, the classifier moves slightly toward the point.

It is imaginable that if we were to repeat this process many times, eventually we will get to a good solution. This procedure doesn’t always get us to the best solution. But in practice, this method often reaches to a good solution as shown in figure 5.23. We call this the _perceptron algorithm_.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-23.png)

Figure 5.23 If we apply the perceptron trick many times, each time picking a random point, we can imagine that we’ll obtain a classifier that classifies most points correctly.

The number of times we run the algorithm is the number of epochs. Therefore, this algorithm has two hyperparameters: the number of epochs, and the learning rate. The pseudocode of the perceptron algorithm follows:

PSEUDOCODE FOR THE PERCEPTRON ALGORITHM

Inputs:

- A dataset of points, labeled 1 and 0
- A number of epochs, _n_
- A learning rate _η_

Output:

- A perceptron classifier consisting of a set of weights and a bias that fits the dataset

Procedure:

- Start with random values for the weights and bias of the perceptron classifier.
- Repeat many times:
    - Pick a random data point.
    - Update the weights and the bias using the perceptron trick.

**Return**: The perceptron classifier with the updated weights and bias.

How long should we run the loop? In other words, how many epochs should we use? Several criteria to help us make that decision follow:

- Run the loop a fixed number of times, which could be based on our computing power, or the amount of time we have.
- Run the loop until the error is lower than a certain threshold we set beforehand.
- Run the loop until the error doesn’t change significantly for a certain number of epochs.

Normally, if we have the computing power, it’s OK to run it many more times than needed, because once we have a well-fitted perceptron classifier, it tends to not change very much. In the “Coding the perceptron algorithm” section, we code the perceptron algorithm and analyze it by measuring the error in each step, so we’ll get a better idea of when to stop running it.

Note that for some cases, such as the one shown in figure 5.24, it is impossible to find a line to separate the two classes in the dataset. That is OK: the goal is to find a line that separates the dataset with as few errors as possible (like the one in the figure), and the perceptron algorithm is good at this.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-24.png)

Figure 5.24 A dataset with two classes that are impossible to separate with a line. The perceptron algorithm is then trained to find a line that separates them as best as possible.

Gradient descent

You may notice that the process for training this model looks very familiar. In fact, it is similar to what we did in chapter 3 with linear regression. Recall that the purpose of linear regression is to fit a line as close as possible to a set of points. In chapter 3, we trained our linear regression model by starting with a random line and taking small steps to get closer and closer to the points. We then used the analogy of descending from a mountain (Mount Errorest) by taking small steps toward the bottom. The height at each point in the mountain is the mean perceptron error function, which we defined as the absolute error, or the square error. Therefore, descending from the mountain is equivalent to minimizing the error, which is equivalent to finding the best line fit. We called this process gradient descent, because the gradient is precisely the vector that points in the direction of largest growth (so its negative points in the direction of largest decrease), and taking a step in this direction will get us to descend the most.

In this chapter, the same thing happens. Our problem is a little different because we don’t want to fit a line as close as possible to a set of points. Instead, we want to draw a line that separates two sets of points in the best possible way. The perceptron algorithm is the process that starts with a random line, and it slowly moves it step by step to build a better separator. The analogy of descending from a mountain also works here. The only difference is that in this mountain, the height at each point is the mean perceptron error that we learned in the section “How to compare classifiers? The error function.”

Stochastic and batch gradient descent

The way we developed the perceptron algorithm in this section is by repeatedly taking one point at a time and adjusting the perceptron (line) to be a better fit for that point. This is called an epoch. However, just as we did with linear regression in section “Do we train using one point at a time or many?” in chapter 3, the better approach is to take a batch of points at a time and adjust the perceptron to be a better fit for those points in one step. The extreme case is to take all the points in the set at a time and adjust the perceptron to fit all of them better in one step. In section “Do we train using one point at a time or many?” in chapter 3, we call these approaches _stochastic_, _mini-batch_, and _batch gradient descent_. In this section, we use the formal perceptron algorithm using mini-batch gradient descent. The mathematical details appear in appendix B, section “Using gradient descent to train classification models,” where the perceptron algorithm is described in full generality using mini-batch gradient descent.

## Coding the perceptron algorithm

Now that we have developed the perceptron algorithm for our sentiment analysis application, in this section we write the code for it. First we’ll write the code from scratch to fit our original dataset, and then we’ll use Turi Create. In real life, we always use a package and have little need to code our own algorithms. However, it’s good to code some of the algorithms at least once—think of it as doing long division. Although we usually don’t do long division without using a calculator, it’s good we had to in high school, because now when we do it using a calculator, we know what’s happening in the background. The code for this section follows, and the dataset we use is shown in table 5.5:

- **Notebook**: Coding_perceptron_algorithm.ipynb
    - [https://github.com/luisguiserrano/manning/blob/master/Chapter_5_Perceptron_Algorithm/Coding_perceptron_algorithm.ipynb](https://github.com/luisguiserrano/manning/blob/master/Chapter_5_Perceptron_Algorithm/Coding_perceptron_algorithm.ipynb)

Table 5.5 A dataset of aliens, the times they said each of the words, and their mood.

|   |   |   |
|---|---|---|
|Aack|Beep|Happy/Sad|
|1|0|0|
|0|2|0|
|1|1|0|
|1|2|0|
|1|3|1|
|2|2|1|
|2|3|1|
|3|2|1|

Let’s begin by defining our dataset as a NumPy array. The features correspond to two numbers corresponding to the appearances of _aack_ and _beep_. The labels are 1 for the happy sentences and 0 for the sad ones.

import numpy as np
features = np.array([[1,0],[0,2],[1,1],[1,2],[1,3],[2,2],[2,3],[3,2]])
labels = np.array([0,0,0,0,1,1,1,1])

This gives us the plot in figure 5.25. In this figure, the happy sentences are triangles, and the sad ones are squares.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-25.png)

Figure 5.25 The plot of our dataset. Triangles are happy aliens, and squares are sad aliens.

Coding the perceptron trick

In this section, we code the perceptron trick. We’ll code it using stochastic gradient descent (one point at a time), but we could also code it using either mini-batch or batch gradient descent. We start by coding the score function and the prediction. Both functions receive the same input, which is the weights of the model, the bias, and the features of one data point. The score function returns the score that the model gives to that data point, and the prediction function returns a 1 if the score is greater than or equal to zero and a 0 if the score is less than zero. For this function we use the dot product defined in the section “Plotting the error function and knowing when to stop running the algorithm” in chapter 3.

def score(weights, bias, features):
    return np.dot(features, weights) + bias ❶

❶ Calculates the dot product between the weights and the features, adds the bias, and applies the step function

To write the prediction function, we first write the step function. The prediction is the step function of the score.

def _step_(x):
    if x >= 0:
        return 1
    else:
        return 0

def prediction(weights, bias, features):
    return _step_(score(weights, bias, features)) ❶

❶ Looks at the score, and if it is positive or zero, returns 1; if it is negative, returns 0

Next, we code the error function for one point. Recall that the error is zero if the point is correctly classified and the absolute value of the score if the point is misclassified. This function takes as input the weights and bias of the model and the features and label of the data point.

def error(weights, bias, features, label):
    pred = prediction(weights, bias, features)
    if pred == label: ❶
        return 0
    else: ❷
        return np.abs(score(weights, bias, features))

❶ If the prediction is equal to the label, then the point is well classified, which means the error is zero.

❷ If the prediction is different from the label, then the point is misclassified, which means the error is equal to the absolute value of the score.

We now write a function for the mean perceptron error. This function calculates the average of the errors of all the points in our dataset.

def mean_perceptron_error(weights, bias, features, labels):
    total_error = 0
    for i in range(len(features)): ❶
        total_error += error(weights, bias, features[i], labels[i])
    return total_error/len(features) ❷

❶ Loops through our data, and for each point, adds the error at that point, then returns this error

❷ The sum of errors is divided by the number of points to get the mean perceptron error.

Now that we have the error function, we can go ahead and code the perceptron trick. We’ll code the condensed version of the algorithm found at the end of the section “The perceptron trick.” However, in the notebook, you can find it coded in both ways, the first one using an `if` statement that checks whether the point is well classified.

def perceptron_trick(weights, bias, features, label, learning_rate = 0.01):
    pred = prediction(weights, bias, features)
    for i in range(len(weights)):
        weights[i] += (label-pred)*features[i]*learning_rate ❶
    bias += (label-pred)*learning_rate
    return weights, bias

❶ Updates the weights and biases using the perceptron trick

Coding the perceptron algorithm

Now that we have the perceptron trick, we can code the perceptron algorithm. Recall that the perceptron algorithm consists of starting with a random perceptron classifier and repeating the perceptron trick many times (as many as the number of epochs). To track the performance of the algorithm, we’ll also keep track of the mean perceptron error at each epoch. As inputs, we have the data (features and labels), the learning rate, which we default to 0.01, and the number of epochs, which we default to 200. The code for the perceptron algorithm follows:

def perceptron_algorithm(features, labels, learning_rate = 0.01, epochs = 200):
    weights = [1.0 for i in range(len(features[0]))] ❶
    bias = 0.0
    errors = [] ❷
    for epoch in range(epochs): ❸
        error = mean_perceptron_error(weights, bias, features, labels) ❹
        errors.append(error)
        i = random.randint(0, len(features)-1) ❺
        weights, bias = perceptron_trick(weights, bias, features[i], labels[i])❻
    return weights, bias, errors

❶ Initializes the weights to 1 and the bias to 0. Feel free to initialize them to small random numbers if you prefer.

❷ An array to store the errors

❸ Repeats the process as many times as the number of epochs

❹ Calculates the mean perceptron error and stores it

❺ Picks a random point in our dataset

❻ Applies the perceptron algorithm to update the weights and the bias of our model based on that point

Now let’s run the algorithm on our dataset!

perceptron_algorithm(features, labels)
**Output:** ([0.6299999999999997, 0.17999999999999938], -1.0400000000000007)

The output shows that the weights and bias we obtained are the following:

- Weight of _aack_: 0.63
- Weight of _beep_: 0.18
- Bias: –1.04

We could have a different answer, because of the randomness in our choice of points inside the algorithm. For the code in the repository to always return the same answer, the random seed is set to zero.

Figure 5.26 shows two plots: on the left is the line fit, and on the right is the error function. The line corresponding to the resulting perceptron is the thick line, which classifies every point correctly. The thinner lines are the lines corresponding to the perceptrons obtained after each of the 200 epochs. Notice how at each epoch, the line becomes a better fit for the points. The error decreases (mostly) as we increase the number of epochs, until it reaches zero at around epoch 140, meaning that every point is correctly classified.

![](https://learning.oreilly.com/api/v2/epubs/urn:orm:book:9781617295911/files/Images/5-26.png)

Figure 5.26 Left: The plot of our resulting classifier. Notice that it classifies each point correctly. Right: The error plot. Notice that the more epochs we run the perceptron algorithm for, the lower the error gets.

That is the code for the perceptron algorithm! As I mentioned before, in practice, we don’t normally code algorithms by hand, but we use a package, such as Turi Create or Scikit-Learn. This is what we cover in the next section.

Coding the perceptron algorithm using Turi Create

In this section, we learn to code the perceptron algorithm in Turi Create. The code is in the same notebook as the previous exercise. Our first task is to import Turi Create and create an SFrame with our data from a dictionary as follows:

import turicreate as tc

datadict = {'aack': features[:,0], 'beep':features[:,1], 'prediction': labels}
data = tc.SFrame(datadict)

Next, we create and train our perceptron classifier, using the `logistic_classifier` object and the `create` method, as shown in the next code. The inputs are the dataset and the name of the column containing the labels (target).

perceptron = tc.logistic_classifier.create(data, target='prediction')

Output:

+-----------+----------+--------------+-------------------+
| Iteration | Passes   | Elapsed Time | Training Accuracy |
+-----------+----------+--------------+-------------------+
| 1         | 2        | 1.003120     | 1.000000          |
| 2         | 3        | 1.004235     | 1.000000          |
| 3         | 4        | 1.004840     | 1.000000          |
| 4         | 5        | 1.005574     | 1.000000          |
+-----------+----------+--------------+-------------------+
SUCCESS: Optimal solution found.

Notice that the perceptron algorithm ran for four epochs, and in the last one (in fact, in all of them), it had a training accuracy of 1. This means every point in the dataset was correctly classified.

Finally, we can look at the weights and bias of the model, using the following command:

perceptron.coefficients

The output of this function shows the following weights and bias for the resulting perceptron:

- Weight of _aack_: 2.70
- Weight of _beep_: 2.46
- Bias: –8.96

These are different results from what we obtained by hand, but both perceptrons work well in the dataset.

## Applications of the perceptron algorithm

The perceptron algorithm has many applications in real life. Virtually every time we need to answer a question with yes or no, where the answer is predicted from previous data, the perceptron algorithm can help us. Here are some examples of real-life applications of the perceptron algorithm.

Spam email filters

In a similar way as we predicted whether a sentence is happy or sad based on the words in the sentence, we can predict whether an email is spam or not spam based on the words in the email. We can also use other features, such as the following:

- Length of the email
- Size of attachments
- Number of senders
- Whether any of our contacts is a sender

Currently, the perceptron algorithm (and its more advanced counterparts, logistic regression and neural networks) and other classification models are used as a part of spam classification pipelines by most of the biggest email providers, with great results.

We can also categorize emails using classification algorithms like the perceptron algorithm. Classifying email into personal, subscriptions, and promotions is the exact same problem. Even coming up with potential responses to an email is also a classification problem, except now the labels that we use are responses to emails.

Recommendation Systems

In many recommendation systems, recommending a video, movie, song, or product to a user boils down to a yes or no answer. In these cases, the question can be any of the following:

- Will the user click on the video/movie we’re recommending?
- Will the user watch the entire video/movie we’re recommending?
- Will the user listen to the song we’re recommending?
- Will the user buy the product we’re recommending?

The features can be anything, from demographic (age, gender, location of the user), to behavioral (what videos did the user watch, what songs did they hear, what products did they buy?). You can imagine that the user vector would be a long one! For this, large computing power and very clever implementations of the algorithms are needed.

Companies such as Netflix, YouTube, and Amazon, among many others, use the perceptron algorithm or similar, more advanced classification models in their recommendation systems.

Health care

Many medical models also use classification algorithms such as the perceptron algorithm to answer questions such as the following:

- Does the patient suffer from a particular illness?

- Will a certain treatment work for a patient?

The features for these models will normally be the symptoms the patient is suffering and their medical history. For these types of algorithms, one needs very high levels of performance. Recommending the wrong treatment for a patient is much more serious than recommending a video that a user won’t watch. For this type of analysis, refer to chapter 7, where we talk about accuracy and other ways to evaluate classification models.

Computer vision

Classification algorithms such as the perceptron algorithm are widely used in computer vision, more specifically, in image recognition. Imagine that we have a picture, and we want to teach the computer to tell whether the picture contains a dog. This is a classification model in which the features are the pixels of the image.

The perceptron algorithm has decent performance in curated image datasets such as MNIST, which is a dataset of handwritten digits. However, for more complicated images, it doesn’t do very well. For these, one uses models that consist of a combination of many perceptrons. These models are aptly called multilayer perceptrons, or neural networks, and we learn about them in detail in chapter 10.

## Summary

- Classification is an important part of machine learning. It is similar to regression in that it consists of training an algorithm with labeled data and using it to make predictions on future (unlabeled) data. The difference from regression is that in classification, the predictions are categories, such as yes/no, spam/ham, and so on.
- Perceptron classifiers work by assigning a weight to each of the features and a bias. The score of a data point is calculated as the sum of products of the weights and features, plus the bias. If the score is greater than or equal to zero, the classifier predicts a yes. Otherwise, it predicts a no.
- For sentiment analysis, a perceptron consists of a score for each of the words in the dictionary, together with a bias. Happy words normally end up with a positive score, and sad words with a negative score. Neutral words such as _the_ likely end up with a score close to zero.
- The bias helps us decide if the empty sentence is happy or sad. If the bias is positive, then the empty sentence is happy, and if it is negative, then the empty sentence is sad.
- Graphically, we can see a perceptron as a line trying to separate two classes of points, which can be seen as points of two different colors. In higher dimensions, a perceptron is a hyperplane separating points.
- The perceptron algorithm works by starting with a random line and then slowly moving it to separate the points well. In every iteration, it picks a random point. If the point is correctly classified, the line doesn’t move. If it is misclassified, then the line moves a little bit closer to the point to pass over it and classify it correctly.
- The perceptron algorithm has numerous applications, including spam email detection, recommendation systems, e-commerce, and health care.

## Exercises

EXERCISE 5.1

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

EXERCISE 5.2

Consider the perceptron model that assigns to the point (_x_1, _x_2) the prediction _ŷ_ = _step_(2_x_1 + 3_x_2 – 4). This model has as a boundary line with equation 2_x_1 + 3_x_2 – 4 = 0. We have the point _p_ = (1, 1) with label 0.

1. Verify that the point _p_ is misclassified by the model.
2. Calculate the perceptron error that the model produces at the point _p_.
3. Use the perceptron trick to obtain a new model that still misclassifies _p_ but produces a smaller error. You can use _η_ = 0.01 as the learning rate.
4. Find the prediction given by the new model at the point _p_, and verify that the perceptron error obtained is smaller than the original.

EXERCISE 5.3

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
    
3. Show that there is no perceptron that models the XOR gate, given by the following dataset:
    
    |   |   |   |
    |---|---|---|
    |_x_1|_x_2|_y_|
    |0|0|0|
    |0|1|1|
    |1|0|1|
    |1|1|0|