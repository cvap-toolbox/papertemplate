# Experiments

## Data Collection
We perform the collection of object data using a Kinect camera with objects placed on a flat surface in front of the robot. The robot observes and segments out the object and then records features over it. Each object is associated with a binary label indicating if it affords an action or not. In addition the robot might be provided with a k-NN ordering for each of the objects in the demonstrated set, where $k$ can vary for each object and $k=1,2,3..$. 

## Learning the Distance Metric
We preprocess the non-histogram features by centering and scaling to unit variance. To evaluate the learning we run the optimization a 100 times using random splits of the collected data with a ratio of $70/30$ training-test data. The biggest drawback of the LMCA algorithm is that the parameter learning is done by cross-validation. Due to the non-convexity and small data point to parameter ratio the LMCA is quite sensitive to initialization and overfits easily. Cross-validation thus is best done using leave-one-out which is quite costly even though we use a constraint activation similar to \cite{Weinberger:2009to} to reduce computation.

A less expensive approach is to keep track of the ratio between the two error terms and the k-NN leave-one-out error on the training dataset. A high ratio and zero leave-one-out error almost always indicates overfitting. As for the $\beta$ parameter we can again think of the analogy between $D$ regression functions, since we have standardized the dataset a good initial guess is $\beta=D$.

