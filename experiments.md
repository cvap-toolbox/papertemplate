# Experiments
We present results that shows (i) How affordance classification is enhanced by metric learning, (ii) How incremental learning affects classification, (iii) How the learned transform can ground the affordance in the feature representation and how that can be visualized on the object, and (iv) How the classification and grounding is affected by a human ordering of object similarity.

## Affordances \& Data Collection
We define the affordances to be reasonable from a human perspective but also not to stretch too much from what is possible for the robot to read from its sensors. For example we use a Kinect1 camera to collect data which is quite sensitive and noisy both in 2D and depth, this limits the resolution and measurements of the point cloud of the object. We choose the following affordances 

\small

* **Hangable** - All objects that affords hanging such as cups with ears. 
* **Putable** - All objects that affords containing something.
* **Rollable** - All objects that affords rolling on the table such as cups without ears, bottles, etc.
* **Lift Top** - All objects that has a top that affords removing such as bottles, pens, etc.
* **Tool** - All objects that affords some kind of tool use this might be a spatula, hammer, screwdriver, etc. 
* **Brushing** - All objects that affords brushing. 
* **Hammering** - All objects that affords hammering this includes a hammer, a screwdriver, a brush, that is, objects that be replaced if a hammer is not available. 
* **Stirring** - All objects that affords stirring again spatulas, or other objects that possibly could be used for stirring.
* **Spraying** - All objects that affords spraying, that is, spray bottles. 
* **Scraping** - All objects that affords some kind of scraping action such as window scraper, etc. 
* **Writeable** - All objects that affords writing, that is, pens. 
* **Drinking** - All objects that affords drinking from such as bottles, cups, etc. Here we define some objects as not drinkable from  social conventions or direct harm, even though they technically affords it.
* **Opening** - All objects that affords opening, that is all food item, boxes, bottles, etc. 
* **Squeezing** - All objects that affords squeezing something out them such as shampoo bottles, etc. 
* **Playing** - All objects that affords some kind of playing such as maracas, some balls and toys.

\normalsize

Our dataset consists of a set of 103 diverse instances, some are the same object but from different viewpoints, such as different tools, cups, bottles, balls, boxes, pots, cans, cleaning fluids, etc. Due to space limitations a link is provided to all images and point-clouds of the dataset.

We perform the collection of object data using a Kinect camera with objects placed on a flat surface in front of the robot. The robot observes and segments out the object and then records features over it. Each object is associated with a binary label indicating if it affords an action or not. In addition the robot might be provided with a human k-NN ordering for each of the objects in the demonstrated set, where $k$ can vary for each object.

## Learning the Distance Metric
We preprocess the features by centering and scaling to unit variance. To evaluate the learning we run the optimization a 100 times using random splits of the collected data with a ratio of $70/30$ training-test data. Due to the non-convexity and number data points to parameters ratio the LMCA is quite sensitive to initialization and overfits easily. Cross-validation for parameter learning is thus best done using leave-one-out which is quite costly even though we use a constraint activation similar to \cite{Weinberger:2009to} to reduce the computational cost.

A less expensive approach is to keep track of the ratio between the two error terms and the k-NN leave-one-out error on the training dataset. A low ratio and zero leave-one-out error almost always indicates overfitting. As for the $\beta$ parameter we can again think of the analogy between $D$ regression functions, since we have standardized the dataset a good initial guess is $\beta=D$.

## Affordance Classification
Affordance classification accuracy and standard deviation can be found in table \ref{fig:affordance_table1}. As we can see the accuracy is above $80\%$ for most of the affordances, LMCA outperforming or scoring equal to k-NN on all affordances. The conclusion we can draw from this is that classification is relatively simple when we have the right representation and even more simple when exactly the right features are selected. Further on, we recognize that  

### Human Provided k-NN 
LMCA can use any distance measure for computing the $k$ nearest neighbors graph that is used in Eq.\ref{lossfun} to learn the metric. We use the $l_2$ distance for initialization, as that is what is used in the loss function. Another approach, is to let a human demonstrator set the $k$ nearest neighbors for each object. This might lead to an ordering of the neighbors that does not reflect the $l_2$ distance but will provide more valuable information into what features are relevant. 

### Incremental Learning
As mentioned in the introduction experiments by \cite{POSNER:1967ef} and others using distorted patterns have shown that the categorization process for humans happens in a continuum. In the beginning individual exemplars are remembered but as more examples are introduced a generalization process takes place. We are therefore interested in how the affordance transforms changes as more examples are introduced.





\input{fig_affordance_classification}






