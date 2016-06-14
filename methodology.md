# Methodology
A straight forward approach to categorization and measuring object to category distances would be to use a k-NN classifier with a Euclidian metric. However, with few data points a comparison based approach like k-NN is sensible to outliers and comparisons in high dimensions are complicated by the curse of dimensionality. In addition the intrinsic metric of the data might not be reflected by the Euclidian metric.

A more sensible approach would therefore be to learn the metric from the data and do it such that irrelevant features are discounted. To this end we employ a specific set of metric learning techniques, called Mahalanobis metric learning, that learns a transform of the data such similar items are close and non-similar items are far away.

\input{metric_learning_explanation}

## Mahalanobis Metric Learning
Mahalanobis metric learning algorithms are a set of metric learning algorithms, that try to learn a positive semidefinite matrix (PSD), $\mathbf{M}$, such that a valid metric can be specified by,
 
 \begin{equation}
	D(x,y) = (x-y)^{T} \mathbf{M} (x-y) = \parallel \mathbf{L}(x-y) \parallel^{2}_{2} \enspace ,
\end{equation}

where the second equality follows since $\mathbf{M}$ is PSD and can be considered a gramian matrix, that is, $\mathbf{M}=\mathbf{L}^{T}\mathbf{L}$. Applying $\mathbf{M}$ to the Euclidian metric is thus equivalent to applying a linear transform and then computing the Euclidian distance. The common theme for these metric learning algorithms is that they work on class labeled data trying to find a matrix that pushes similar items closer while pushing non-similar items further away. See Kulis et al. \cite{Kulis:2012tx} for an excellent survey. 

In our previous work \cite{Hjelm:2015hw} we used the Large Margin Nearest Neighbor (LMNN) model due to Weinberg et al. \cite{Weinberger:2009to}, to learn metrics over grasp affordance features. We forced the learned matrix, $\mathbf{M}$, to be diagonal, meaning that it discarded information about correlations between features in favor of reducing overfitting and enhancing accuracy. The diagonal could be considered feature weights and although it gave favorable results in terms of feature selection and grasp execution, it was unsatisfactory in modeling dependencies between features. 

Here we instead use the Large Margin Component Analysis (LMCA) model due to Torresani et al. \cite{Torresani:2006wb}. \cite{Torresani:2006wb} formulate an objective function similar to \cite{Weinberger:2009to} but where the optimization is carried out over $\mathbf{L}$ using gradient descent. 

\small 
\begin{equation}
\label{lossfun}
\begin{aligned}
& \epsilon(\mathbf{L}) = \sum\limits_{i,i \leadsto j} ||\mathbf{L} (x_i-x_j)||^{2}  \\
&  + c \sum\limits_{i,i \leadsto j,l}(1-y_{il})h(||\mathbf{L} (x_i-x_j)||^{2} - || \mathbf{L} (x_i-x_l)||^{2} +1 )  \\
\end{aligned}
\end{equation}

\begin{equation}
\label{lossfunderivative}
\begin{aligned}
& \frac{d\epsilon(\mathbf{L})}{d\mathbf{L}} = 2 \mathbf{L} ( \sum\limits_{i,i \leadsto j} (x_i-x_j)(x_i-x_j)^{T}  \\
&  + c \sum\limits_{i,i \leadsto j,l}(1-y_{il}) [(x_i-x_j)(x_i-x_j)^{T} - (x_i-x_l)(x_i-x_l)^{T} ]  \\
& h'(||\mathbf{L} (x_i-x_j)||^{2} - || \mathbf{L} (x_i-x_l)||^{2} +1 ) ) \\
\end{aligned}
\end{equation}.
\normalsize

Here $i \leadsto j$ means the $k$ nearest neighbors of $i$ belonging to the same class, $y_{il}$ is binary variable that is one if $i$ and $l$ have the same label and zero otherwise, $c$ is a constant and  $h$ is the smooth hinge loss \cite{Rennie:2005ds}. The first term thus penalizes large class neighbor distances while the second term penalizes any non-class members being closer than the $k$ nearest neighbors by a margin.

By optimizing over $\mathbf{L}$ instead of $\mathbf{M}$, for a dataset of dimension $D$, we can let $\mathbf{L}$ be $d \times D$ where $d$ is a variable that can be set by cross-validation. Learning $\mathbf{L}$ will then amount to learning $dD$ variables instead of $D^2$ when learning $M$. This reduces overfitting for small but high-dimensional datasets and in addition  dimensionality reduction is learned simultaneously while in \cite{Weinberger:2009to} the dimensionality reduction is done using PCA before learning $\mathbf{M}$. 

The drawback of this approach is that since the objective function is no longer convex we will discover local minima. \cite{Torresani:2006wb} showed empirically that this was not a problem since the algorithm consistently converged to adequate solutions and the kernelized version outperformed k-NN and LMNN on several benchmark datasets.

Learning matrices that re-represents the input data opens up for analysis of the correlation between different features by considering $M$. The magnitude of the column vectors of $L$ also works as an importance measure for each of the features. If we are interested in learning which features are important for a specific affordance we would want all columns in the $L$ matrix connected to non-important features to have zero or close to zero magnitude. 

We therefore introduce the elastic net regularization of \cite{Cimpoi:2015eg}, which penalizes the column vectors of $L$ using the sum of the $L_1$ and $L_2$ norm. If we view the column vectors of $L$ in Eq. \ref{lossfun} as a form of $D$ linear regression functions that we simultaneously optimize it would make sense to regularize them for cancelation effects and to curb any blowing up of a specific column. From our experience LMNN is quite sensitivy to the parameters and easily overfits when having few data points compared to the number of parameters, that is, $N \ll Dd$, and using regularization helps to avoid some of it. In addition the elastic net regularization has the benefit of keeping groups of correlated variables whereas the $l_1$ regularization usually keeps only one of them \cite{Cimpoi:2015eg}.  

The modified elastic net regularized loss function now has the form,

\small 
\begin{equation}
\label{lossfun}
\begin{aligned}
& \epsilon_{new}(\mathbf{L}) = \lambda_1 \sum_{j=1}^{D} \left\lVert L_j  \right\rVert_1 + \lambda_2 \sum_{j=1}^{D} \left\lVert L_j  \right\rVert_{2}^{2} +   \epsilon(\mathbf{L}) \\
\end{aligned}
\end{equation}

\begin{equation}
\label{lossfunderivative}
\begin{aligned}
& \frac{d\epsilon_{new}(\mathbf{L})}{d\mathbf{L}} =   \lambda_1  \tilde{L}_1 + \lambda_2  \tilde{L}_2 + \frac{d\epsilon(\mathbf{L})}{d\mathbf{L}} \\
& (\tilde{L}_1)_{ij} = sign(L_{ij}), \; \; \; (\tilde{L}_2)_{ij} = 2 L_{ij}.
\end{aligned}
\end{equation}
\normalsize

When $L_{ij}$ is zero the derivate of the $l_1$ norm is singular. We define its derivate at zero as,

\begin{equation}
 ( \tilde{L}_1 )_{ij}_ =
  \begin{cases}
    1   & \frac{d\epsilon(\mathbf{L})}{d\mathbf{L}} > \lambda \\
    -1  & \frac{d\epsilon(\mathbf{L})}{d\mathbf{L}} < \lambda \\
  \end{cases}
\end{equation}

\begin{comment}
[We should maybe investigate if the penalization has the same effect on the eigenvalues as ridge regression ]
\end{comment}


## Features
We want to use features that have at least a weak semantic meaning to a human observer and that are as generic as possible. Affordances can from a human perspective be understood in both global and local properties of the object we therefore try to cover both. Due to lack of space we will provide a short list with motivations when needed.

\small

* **Object Volume** - The volume of the convex hull enclosing the object.
* **Primitive Shape** - Scoring function for how well the object resembles spherical, cylindrical, or cuboid form.
* **Elongatedness** - The ratio of the minor axes of the object to the major axis. 
* **Opening** - Wether our opening detection algorithm detects an opening.
* **Intensity and Gradient Filters** - We apply gradient filters of first, second order over the 2D window containing the object along, in addition we extract the intensity values of the image. Since pixels correspond to the points on the point cloud of the object we map the values on to each point. We then compute a count normalized histograms (CNH) for all the points of the object for each of the filter values and the intensity values.
* **Color Quantization** - We segment the image using the segmentation algorithm of \cite{Felzenszwalb:2004vi} and encode each patch with its closest color from a chart of 15 different colors, in CIELab space. Each point in the point cloud of the object thus gets a quantized color assigned to it. We then form a CNH over all the points of the object.  
* **Color Uniformity** - We compute the entropy, mean and variance of the color quantization.
* **Bag of Words Histogram over Fast Point Feature Histogram (FPFH)** \cite{Rusu:2009hf} - We encode the local curvature of the object using a Bag of Words model over the FPFH features of the point cloud using a small set of 40 keywords. We then extract the histogram of the keywords for the object. 
* **Texture** - We employ a similar approach to \cite{Cimpoi:2015eg} by learning a Fisher vector representation from dense SIFTs over a set of labeled images containing the following textures: glass, carton, porcelain, metal, plastic and wood. We also extract the output of the fifth layer of a GooleLeNet CNN concatenating it with the Fisher vectors. These two features complement each other, the Fisher vector trained on textures, and the GooleLeNet in that certain objects are more likely to be made of a specific material. We use the final feature vector to train 6 1-against-$K$ linear SVMs. The output from the 6 SVMs when classifying the object can be considered as a score of how similar each object is to one of the textures and we use this as the feature vector. 

\normalsize


