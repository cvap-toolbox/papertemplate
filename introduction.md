# Introduction
Imagine you are part of an experiment where you are shown a set of patterns, and you are trained to recognize them. Each pattern is a distorted version of some prototype pattern. Next you are shown a new set of distorted patterns, and you are told to classify the patterns as seen belonging to one of the prototypes. This is a classic experiment done by Posner et. al \cite{POSNER:1967ef} to probe how humans learn to abstract visual stimuli and form categories. The experiments show how categories form in a continuum rather than in distinct classes. The experiment also show that sufficiently distinct members are treated as individual exemplars rather belonging to some specific category. 

For a robotic agent acting in an unstructured environment learning object affordances is similar to the pattern experiment — there is not much need for object individuation other than when encountering unique objects — the key is rather to understand the underlying common pattern among the objects that form the category. To differentiate and to categorize objects we need to learn how to measure the similarity to the patterns we have pervasively observed and what in the pattern to measure for different categories.

From a learning perspective this boils down to classification and feature selection. Most approaches have focused on either of the two. In \cite{Stoytchev:2005il,Chao:2011id,Niekum:us} the authors tries to learn pre and post-conditions of the features that affects the outcomes from applying the actions. In Yuruten et al. \cite{Yuruten:2013hr} the authors learn a robot to match descriptive nouns to object features using feature selection with a radial based SVM. \cite{Stark:2008bx}...

From this perspective our approach is more holistic, we consider learning feature selection and classification as an integral part of each other. We realize firstly that the distance between objects that are similar should be close in feature space. Feature dimensions are high-dimensional and any distance measure will be hampered by all points being close in high dimensions, due to this we would like to discount features that are irrelevant for the categorization at hand. To this end we employ a metric learning algorithm, that learns a transform that pushes similar items together and non-similar far away. Since most features are redundant for a specific category the transform can also be forced to project onto a lower dimensional feature space. In addition we introduce a regularizer in the learning algorithm that penalizes transforms that are non-sparse and which induces feature selection. 

Much research argue that learning affordances, should be done through grounding in the sensorimotor system of the agent. Affordances recognition and discovery is as much tied to interaction with the object as it is to visual stimuli. In Sahin et al. \cite{Sahin:2007gr} for example, the authors define an agent centric definition of affordances that relies of the effects of interaction with the world. Much of the research on affordance learning follows this paradigm — that only by forming its own representation through grounding in its own sensory input will the agent be able to fully understand the affordance. 

Most of the affordance learning through interaction have so far been focused on simple affordances such as pushing, rolling, etc. For example in Modayil et. al \cite{Modayil:2008it} an autonomous robot learns about the world through random motor babbling, observing changes in the perceived world and makes goal oriented plans based on matching the learned actions to a given plan.

Learning for more complex tasks on the other hand, such as interaction with everyday objects learning is more complicated. In robotics the problem is usually approached by Learning from Demonstration (LfD) as this is the way humans also learns complex tasks. Here again, learning is usually tied to letting the robot match the observation to the action through its own sensors, such as in \cite{Faria:2014ug}[Vet inte om det är bra citering]. 

One integral part in performing complex affordances are grasping and manipulation, research have mostly focused on the grasping of objects regardless of task context \cite{Bohg:H95zG3Ya}. However, steps have been taken towards learning task dependent grasp selection by LfD of human priors on grasp placement \cite{Hjelm:2015hw}. 

Understanding of how an object should be manipulated thus lays as much in learning complex motor commands as recognizing how an object is best manipulated. Understanding what in the feature representation of an object makes the object afford the action is key to understanding this. From this understanding it also follows that an agent can greatly reduce the space of possible manipulation actions and gain a deeper understanding of the affordance.
Previous efforts have been part in grasp planning models and focused on different forms of semantic segmentation. In Aleotti et al. \cite{Aleotti:2011hc} the authors segments objects as graphs where each node can be linked to specific grasp manipulations. Antanas et al. \cite{Antanas:2014uo} divides objects into tools which have segments handles and usable areas, and other objects which have segments that are bottom, middle and top of the object as well as possible handles. The authors use these segmentations to compute probabilities of graspable parts. Similarly Faria et al. \cite{Faria:2014ug} segments objects using the main axis of the object and a clustering of the local curvature using a Gaussian Mixture Model (GMM) which can be associated with particular types of grasps.

Our model breaks with these approaches by assuming that affordances of objects can both be formulated around global features such as the elongatedness of the object, the volume, etc., or local features such as the specific color consistency across objects, single color caps for examples, or the local curvature of mug handles. And instead of tuning a segmentation algorithm to fit our purposes we want the agent to be able to discover the common denominator in the feature space of the objects sharing a specific affordance. This means that the semantic meaning of the affordance will be grounded in the sensory input of the agent. This grounding will have two key components — the transform of the feature space and the instances in the transformed feature space. 

The approach most similar to ours is by Hermans et al. \cite{Hermans:2011vz} they train classifiers to recognize key attributes of objects such as size, shape, color, material, and weight. Affordances are then classified with the attributes as inputs. The authors compare with a SVM trained directly on the feature space which performs comparably or better. This is explained in terms of the feature space containing information not directly explainable as any specific semantic attribute. We also want to remark that if we follow the idea that affordances should be grounded in the sensory input of the agent we can  conclude that imposing a human structure on top of how objects are interpreted might not help the agent but rather confuse it; especially when the individual features are extremely noisy. 

We also make use of discretization in the creation of certain features but as it always means a trade-off between discarding and keeping certain information we try to avoid it when possible. And contrary to \cite{Hermans:2011vz} the information explaining the affordance is reflected in the the transform and directly relatable to the input. 

As mentioned above we are not interested in object individuation as we think this is a hinderance to truly understanding affordances. We instead want to formulate the understanding around the feature transform. This enables us to, in the case of a local feature, map the affordance to a specific portion of the object. As a special case of understanding common structures we show how we can learn a segmentation that captures the important parts of the object. Further on, this makes comparison between affordances possible by computing the distance between the transforms.

As for the transformed instances they are used in this work to classify object affordances using the k-Nearest Neighbors (kNN) and to show how objects are interrelated in the transform space. However, there is nothing stopping us from using an SVM classifier instead, in fact a linear SVM can be shown to give similar results to the kNN classifier under the transform. 


\cite{Nikandrova:2015uu}
\cite{Thomaz:2009uk}
\cite{Griffith:2009cm}
\cite{Stark:2008bx}



\begin{comment}

ONE IDEA IS TO USE SEVERAL BOW REPRESENTATIONS AT DIFFERENT RESOLUTIONS!!



In the future we can expect that skill transfer will happen from robot to human, this will further the need for the agent in understandning for 

Skill-transfer from human to human works in the same manner as in the above experiment. We show some objects, explain the underlying invariant features, similarities and how the action works. The learner can then use the acquired skills to explore and act in the environment using the invariant knowledge to extrapolate and solve tasks. An important point is that that the transfer is not an exact copy due to the difference in semantic and sensory capabilities of the teacher and learner. 

In the future it is reasonable to assume that this skill-transfer will also happen between human and robots. Communication would be greatly enhanced by giving the robot the ability to ground the sensory input in semantic meaning, that is, find the the invariant features of the group and form metrics over these to discriminate which objects belongs to the group or not. Further on, utilizing this learning strategy as opposed to downloading an already trained classifier, would lead to greater adaptability and autonomy as the experience would be grounded in the robots own experience. 

If we go even further we can assume that in the future skill transfer will also happen from robot to human. Here semantic meaning grounded in sensory input  will be the only way to transfer knowledge as there is yet no known way of programming a human brain for learning complex tasks. 

Previous approaches to learning affordances have focused on....

Most of the above mentioned approaches focuses on learning affordances using large datasets that does not discover the invariant features of the object and where the focus has shifted from grounding to model accuracy. These models while useful for detection will not be very helpful in human-robot interaction. 




Lära eigen spectrumet dominanta plotta Renauds 

Hur annorlunda är representationen  för de olika tasken projektion av data punkter overlay. 
Vilka task är korrelerade? 

\end{comment}
