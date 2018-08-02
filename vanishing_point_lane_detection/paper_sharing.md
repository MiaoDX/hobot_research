% Vanishing point, horizon line and lane detection
% MiaoDX 缪东旭 <br>MiaoDX@hotmail.com
% August, 2018

***
## Papers we will talk about

VPGNet: Vanishing Point Guided Network for Lane and Road Marking Detection and Recognition, Seokju Lee, Junsik Kim, and other 8 people,<br> ICCV 2017

GIC, Detecting Vanishing Points using Global Image Context in a Non-Manhattan World, Menghua Zhai, Scott Workman, Nathan Jacobs, CVPR 2016

HLW, Horizon Lines in the Wild, Scott Workman, Menghua Zhai, Nathan Jacobs, BMVC 2016

IGP, Deep Learning for Vanishing Point Detection Using an Inverse Gnomonic Projection, Florian Kluger, Hanno Ackermann, Michael Ying Yang, Bodo Rosenhahn, GCPR 2017

# What is VP

***
## Vanishing point

A vanishing point results from the intersection of projections of a set of parallel lines in the world. ..  VPs can typically be classified as either vertical, there is one such VP, and horizontal, there are often many such VPs. -- GIC

![Normal definition of VP](pics/vp_define1.png){width=55%}

***

A vanishing point is a point where parallel lines in a three-dimensional space converge to a two-dimensional plane by a graphical perspective.<br>
In this paper, “Vanishing Point (VP)” is defined as the nearest point on the horizon where lanes converge and disappear predictively around the farthest point of the visible lane. -- VPGNet

![VPGNet (driving) VP annotation](pics/vpgnet_vp_anno.png){width=55%}

***
**Manhattan-world assumption:** <br>only three mutually orthogonal vanishing directions exist in a scene, as is reasonably common in urban scenes where buildings are aligned on a rectangular grid

**Atlanta-world assumption:** <br>allows multiple non-orthogonal vanishing directions that are connected by a common horizon line, and are all orthogonal to a single zenith

**No specific assumption**


***
# VPGNet

VPGNet: Vanishing Point Guided Network for Lane and Road Marking Detection and Recognition, Seokju Lee, Junsik Kim, and other 8 people,<br> ICCV 2017

***

![](pics/vpgnet_demo1.png){width=55%}


***

* If the network is trained with a thin lane annotation, the information tends to vanish through convolution and pooling layers
* Most of the neural networks require a resized image (usually smaller than original size), the thin annotations become barely visible
* The image is divided into a grid 8×8 and the grid cell is filled with a class label if any pixel from the original annotation lies within the grid cell.

. . .

![](pics/vpgnet_grid_level.png){width=55%}

***
### Dataset

* Polygon/pixel-level annotation
    - We manually annotate corner points of lane and road markings. Corner points are connected to form a polygon which results in a **pixel-level mask** annotation for each object. In a similar manner, each pixel contains a **class label**.
* Grid level projection
* Vanishing point
    - We localize the vanishing point in a road scene where parallel lanes supposedly meet. Depending on the scene, a **difficulty level** (EASY, HARD, NONE) is assigned to every vanishing point.

***
### Architecture

![](pics/vpgnet_net.png){width=75%}

***
* Propose a data layer to induce grid-level annotation that enables training of both lane and road markings simultaneously
* Propose an regression that utilizes a grid-level mask. Points on the grid are regressed to the closest grid cell and combined by a multi-label classification task to represent an object
* For the post-processing, lane classes only use the output of the multi-label task, and road marking classes utilize both grid box regression and multi-label task

***
### VPP (Vanishing Point Prediction Task)


* regression losses 
    - (i.e. L1, L2, hinge losses) that directly calculate pixel distances from a VP
    - difficult to balance the losses with other tasks (object detection/multi-label classification) due to the difference in the loss scale
* cross entropy
    - binary classification method that directly classifies background and foreground
        + Since the vanishing area is drastically smaller than the background, the network is initialized to infer every pixel as background class.

***
The purpose of attaching the VPP task is to improve a scene representation that implies a global context to predict invisible lanes due to occlusions or extreme illumination condition (just like human).


Whole scene should be taken into account to efficiently reflect global information inferring lane locations

* Quadrant mask that divides the whole image into four sections
* Define five channels for the output of the VPP task: one absence channel and four quadrant channels
* Instead of classify the VP, now assign all pixels.

***
![](pics/vpgnet_vp.png){width=45%}

***
### Training

**Trained together**

* The detection task recognizes objects and covers a local context, while the VPP task covers a global context
* VP provides redundant information to the network, leading to marginal lane detection improvement.

. . .

**Two phases**

* VPP task
    - fix the learning rates to zero for every task except the VPP module
* all tasks (including VPP)
    - balance learning rates
        + $$Loss = w_1L_{reg} + w_2L_{om} + w_3L_{ml} + w_4L{vp}$$
        + where $L_{reg}$ is a grid regression L1 loss, $L_{om}$ and $L_{ml}$ and $L_{vp}$ are cross entropy losses in each branch of the network

***
First, $w_1$∼$w_4$ are set to be equal to $1$, and the starting losses are observed. Then, we set the **reciprocal** of these initial loss values to the loss weight so that the losses are uniform.

In the middle of the training, if the scale difference between losses becomes large, this process is repeated
to balance the loss values.

***

### Post processing

***
**Lane**

* multi-label task

. . .

![](pics/vpgnet_post_processing.png){width=45%}

The last step is **quadratic regressions** of the lines from the obtained clusters utilizing the location of the VP. If the farthest sample point of  each lane cluster is close to the VP, we include it in the cluster to estimate a polynomial model.

***
**Road marking**

* grid box regression
* multi-label task

. . .

each class in multi-label -> grids in same class -> merging

crosswalks or safety zones (difficult to define by single box) no merging

***
### Analysis

![](pics/vpgnet_featuremap.png){width=45%}


# GIC

Detecting Vanishing Points using Global Image Context in a Non-Manhattan World, Menghua Zhai, Scott Workman, Nathan Jacobs, CVPR 2016

***
![](pics/gic_overview.png){width=75%}

***


* dominant trend
    - first find candidate vanishing points, then remove outliers by enforcing mutual orthogonality
* Our method reverses this process
    - we propose a set of horizon line candidates and score each based on the vanishing points it contains
* global image context
    - extracted with a deep convolutional network, to constrain the set of horizon line candidates (and zenith vanishing point)








***
## Comments

***
### Quick Review


***
### Speed

The optimization way is slow, too slow to be practical on ADAS systems, the End-to-End can be fast, but it will face the challenge of interpretability.

~~A DIAGRAM is needed~~

VPGNet, NVIDIA GTX Titan X, 20 Hz, forward pass 30 ms, post-processing 20 ms or less
