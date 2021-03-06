---
layout: post
title:  "Faster R-CNNs with Deep Residual Nets"
date:   2018-05-03 
categories: [Deep Learning, Object-detection, Python]
comments : true
---
<!-- for latex like math -->
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]},
  processEscapes: true,
  Tex: { equationNumbers: { autoNumber: "AMS" } }
});
</script>
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS_HTML">
</script>


<ul id="toc"></ul>

---

## Introduction

In this post we will talk about the object detection system using Faster R-CNN propsoed by _Ren et. al._ in 2015. Faster R-CNNs are made up of two modules. The first one is a fully convolutional network called the Region Proposal Network (**RPN**) and the second module is the Fast R-CNN detector that uses the proposed regions for classification. The entire system is one single combined system for object detection. The RPN module tell the R-CNN where to look. The following figure shows the Faster R-CNN.

{% include image.html
   img="/data/faster-r-cnn/images/faster-r-cnn-model.jpg"
   caption="Faster R-CNN is a single network consisting of 2 modules. The RPN modules serves as the 'attention' for the unified network. (Source:https://arxiv.org/abs/1506.01497)"
%}


## Faster R-CNN

Now we talk about the different componenets of the Faster R-CNN in detail

### Region Proposal Networks
An RPN takes as input an image and returns a set of rectangles, these are the object proposals and each of them has a objectness score. This is done using a fully convolutional network, which shares some covolutional layers with a Fast R-CNN object detection network. The regions are generated by using a small network over the convolutional feature map. This feature map is the output of the last shared convolutional layer. The small network takes as input a _n x n_ region of the feature map, which is then mapped to a lower-dimension feature with 256 layers (filters), _256-d_. These are input into two parallel fully connected layer, a box-regression layer (_reg_) and a box-classification layer (_cls_). _Ren et.al_ take the value _n = 3_ for their paper. This mini-network consists of a $n \times n$ convolutional layer, followed by two sibling convolutional $1 
\times 1$ layers, the _reg_ and _cls_ respectively. 

#### Anchors
For each sliding window, the networks makes multiple simulatneous proposals, where is the number of proposals for each sliding window location is _k_. The _reg_ layer gives _4k_ outputs containing the coordinates of the _k_ boxes and the _cls_ layer had _2k_ scores that estimate the probablity of being an object or not object for each proposal. These _k_ proposals are estimated from _k_ reference boxes, which are called _anchors_.  An anchor for a given sliding window, has a scale and aspect ratio associated with it, in their paper _Ren et. al_ have used 3 scales and 3 aspect ratios, this yeilds 9 anchors at each sliding window location. For a convolutional feature map of size $w \times h$ the total number of anchors = $w \times h \times k$.



##### Transition-Invariant Anchors
This way of selecting anchors make them _translation invariant_. This basically means that of one translates an object in an image, the proposal should also translate and the same function would be able to predict the proposal in either location. The translation invariant reduces the model size as well. For example the MultiBox method [2] has a $(4 + 1) \times 800-dimenetional$ fully-connected output layer, where as this method just has $(4 + 2) \times 9$-dimentional convolutional output layer in the case of $k=9$ anchors. Due to smaller datasets, there is also lesser risk of overfitting on samller datasets like the PASCAL VOC.

##### Multiscale Anchors as Regression References
Insted of resizing images for coputing the features at each scale, it is faster to use a sliding window of multiple scales on the feature maps. Ren [1], uses the idea of _pyramid of anchors_, which is simailar to the DPM[], where models with different aspect ratios are trained using different filter sizes, "pyramid of filters". The method classifies objects and regresses bounding boxes based on the achors of different sizes and aspect ratios, on feature maps and filters of a single scale. 
Similar to the Fast-RCNN detector[], Faster-RCNNs use the convolutional features computed on a single scale image, which is hte key component for sharing features without any extra cost related to varying image scales.

### Loss Function
RPNs are trained using the anchors where each of then is assigned a binary lable of being an object or not. A postive lable is assigned to the anchor with the largest intersection-over-Union (IoU) overlap with the ground truth box, or an anchor with IoU greater than 0.7. Both these conditions are required to ensure there are positive anchors for all cases. Negative lables are assigned to a non-positive anchor whoes IoU is less than 0.3 for all the ground-truth boxes. Anchors that reamin unlabelled are not considered in the training process. Thus the objective function is minimized using the following loss function for an image:

$$
L({p_{i}},{t_{i}}) = \frac{1}{N_{cls}}\sum{L_{cls}(p_{i},p_{i}^*)} + \lambda\frac{1}{N_{reg}}\sum{p_{i}^* L_{reg}(t_{i},t_{i}^*)}
$$

### Training 
The RPNs are trained using back-propogation using stochastic gradient descent (SGD).
Each mini-batch is derived from a single image that contains several positive and negative anchors. The mini-batches consist of positive anchors and negative anchors in the ratio of approx $1:1$, if there are less than 128 positive anchors for an image, they are padded with negative anchors.

The shared convolutional layer are intialized using weights from a model trained on ImageNet Classification. The new layers are initialized with random weights taken from a zero mean Gaussian distribution with standard deviation of 0.01. The leaning rate of 0.0001 is used for the first 60k mini-batches and 0.0001 for the next 20k mini-batches on the PASCAL VOC dataset. Momentum of 0.9 and weight decay of 0.0005 has been used.

##### Sharing Feature training for RPN and Fast R-CNN
Now we consider the object-detection CNN which will be utilizing the regions prosposed by the RPNs. For this we use Fast R-CNN [1]. Next we talk about algorithm used to train a combined RPN and Fast R-CNN having shared convolutional layers, as shown in the <a href="#introduction">Introduction</a>.

<!-- Following are some of the ways of training networks with shared layers:

(i) _Alternating training_: This method was used by Faster R-CNNs for all their experiments. In this method, we first train the RPN and use the proposals to train the Fast R-CNN. The weights from the network trained by Fast R-CNN is used to initialize RPN, and this process is iterated. -->

**4-step alternating training** has been used in the Faster R-CNN approach. 
1. The RPN is trained using the several anchors as discussed earlier. This network is initialized with an ImageNet pre-traine model and then trained end-to-end for region proposal task.
2. A separate detection network is now trained by Fast R-CNN, using the proposals generated by the RPN in step 1. This network is also initialized by the the ImageNet pre trained model. 
3. Next, the detector network is used to initizlize the RPN training, the weights on the share convolutional layers are fixed and only the layer unique to the RPN are altered.  Now the layers share some covolutional layers.
4. Keeping the shared convolutional layers fixed for the, the layers unique to the Fast R-CNN are trained.

These step may be repeated for more training, but it leads to negligible improvements.

## Implementation Details
Both the RPN and the object detection network on images with single scale.
Images are rescaled such that their shorted side, $s = 600$ pixels [2]. For anchors, boxes with are $128^2$, $256^2$ and $512^2$ is used with the aspect ratios, 1:1, 1:2 and 2:1. Both ZF net and VGG nets were used for initializing weights for the RPN.
There are anchor boxes which cross the boundaries of the image, during training, such anchors have been ignored. For a typical $1000 \times 600$ image there are approx. 20000 anchors. Ignoring the cross-boundry anchors, this falls to around 6000 anchors per image. if not ignored, the cross-boundary anchors introduce large errors in the objective and the training does not converge. During testing the same RPNs are used, the cross-boundry proposals are clipped to the image boundary.

Some of the proposal are highly overlapping, to reduce this redundancy, a the techique called non-maximum supression (NMS) has been used. The _cls_ score is used for the NMS and the IoU is set at 0.7. This means that if two proposal boxes have a IoU of 0.7, the one with the higher _cls_ score is selected for training. Using NMS does not affect the detection accuracy and also recduces the number of proposals. Using NMS gives around 2000 proposals per image, which is used for training the Fast R-CNN. The top N-ranked regions are used for detection.

<!-- 
## Experiments
### Experiments on PASCAL VOC
### Experiments on MS COCO
 -->





## References
 [1] R. Girshick, “Fast R-CNN,” in IEEE International Conference on Computer Vision (ICCV), 2015

 [2] Ren, Shaoqing, et al. "Faster r-cnn: Towards real-time object detection with region proposal networks." Advances in neural information processing systems. 2015.

 [3] Scalable, High-Quality Object Detection 

## Useful Links 
Paper: <a href="https://arxiv.org/abs/1506.01497" target="_blank">https://arxiv.org/abs/1506.01497</a>
	<br>
Official repo:  <a href="https://arxiv.org/abs/1506.01497" target="_blank">https://github.com/ShaoqingRen/faster_rcnn</a>

---