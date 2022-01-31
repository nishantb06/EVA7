# Assignemnt 14

## ✔ Explain DETR
The design ethos of the entire DETR was that Object Detection problems shouldn’t be as easy as classification. That is we shouldnt have to use so many hand crafted components like Non-Max Suppression or Anchor boxes. So DETR uses transformer architecture that was suggested in Attention is all you need paper and uses it to output a set of N Object queries To predict the class level and bounding box. 

They have uniquely used a bipartite loss which uniquely assigns a prediction to a ground truth object and is invariant to permutation of predicted objects so we can emit them in parallel.Their experiments show that they achieved comparable performance in object detection as compare to faster R-CNN and much better performance in panoptic - segmentation as compare to any other algorithm, just by addiing a simple segmentation head on top of a pretrained DETR.

![Imgur](https://imgur.com/dZxIEIJ.png)

The overall DETR architecture is surprisingly simple and depicted in the above figure it contains three main components which we describe below a CNN backbone to extract a compact feature of presentation and encoder decoder transformer and a simple feedforward network that makes the final detection prediction.

## ✔ Explain Bipartite Loss and why we need it

Bipartite Loss. Why do you need it?

According to me you need it for one simple reason. To prevent multiple prediction of the same bounding box of the same object. Lets say we send in a 100 queries, which means we get a 100 predictions for one image. All bounding boxes of all the objects within that image, no matter what size they are, will be predicted in those 100 queries. Which is why you need to make sure that the number of objects per image in your dataset does not exceed a 100.

> This procedure of finding matching plays the same role as the heuristic assignment rules used to match proposal or anchors to ground truth object in modern detectors. The main differce is that we need to find one-to-one matching for direct set predictions without duplicates.
> 

What this excerpt from the paper means that bipartite loss is meant as replacement of non max suppression used in algortihms like YOLO and Faster-RCNN’s. Except I think bipartite loss is far more computationally viable as it does not calclulate loss of all the permuations.

It is my understanding that loss is calculated in 3 steps.

1. Hungarian Matching of the prediction to the target and thus calculate 
2. After matching is done calculate the hungarian loss  - $L_{hungarian}$ of that specific pairwise permuatation
3. to the above step we need to calcluate $L_{box}$ which scores how good the predicted bounding boxes are. It is a mxture of the L1 loss and IOU loss.

### ✔ Explain Object Queries

Object Queries are simply slots for the decoder to fill in with bounding boxes. The number of object queries should always be less than the maximum number of objects in each image and therefore the authors have chosen a large number - 100. Theoretically they are all zeros but are refined through the course of training and are fixed for evaluation. No Geometric priors are added between them unlike positional encodings

> *The difference with the original transformer is that the DETR model decodes the NNN objects in parallel at each decoder layer, while Vaswani et al. used an autoregressive model that predicts the output sequence one element at a time. Since the decoder is also permutation invariant, the NNN input embeddings must be different to produce different results. These learnt positional encodings are called object queries and as can be seen in Figure-5, similar to the Encoder, these object queries are also added to the input at each attention layer in the Decoder.*
> 

The following excerpt from the paper states that object queries are sent in all at once unlike the traditional decoders where they are sent in sequentially.

This [Github Issue](https://github.com/facebookresearch/detr/issues/178) also states that the same object query is used to predict an object in the same area of the image!! See the [official talk](https://www.youtube.com/watch?v=utxbUlo9CyY&t=326s) for DETR ECCV presentation.


## Explain Encoder Decoder Architecture

## Results
![Imgur](https://imgur.com/40a4Bdn.png)