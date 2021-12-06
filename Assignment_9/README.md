# ResNets and Higher Rece

## Reproducing the results and model of current [SOTA](https://github.com/apple/ml-cifar-10-faster) for training time -CIFAR10 dataset according to [DawnBench](https://dawn.cs.stanford.edu/benchmark/CIFAR10/train.html)

**Aim**

Write a custom ResNet architecture for CIFAR10 that has the following architecture:
- PrepLayer - Conv 3x3 s1, p1) >> BN >> RELU [64 kernels]
- Layer1 -
  - X = Conv 3x3 (s1, p1) >> MaxPool2D >> BN >> RELU [128 kernels]
  - R1 = ResBlock( (Conv-BN-ReLU-Conv-BN-ReLU))(X) [128 kernels] 
  - Add(X, R1)
- Layer 2 -
   - Conv 3x3 [256 kernels]
   - MaxPooling2D
   - BN
   - ReLU
- Layer 3 -
   - X = Conv 3x3 (s1, p1) >> MaxPool2D >> BN >> RELU [512 kernels]
   - R2 = ResBlock( (Conv-BN-ReLU-Conv-BN-ReLU))(X) [512 kernels]
   - Add(X, R2)
   - MaxPooling with Kernel Size 4
   - FC Layer 
   - SoftMax
- Uses One Cycle Policy such that:
   - Total Epochs = 24
   - Max at Epoch = 5
   - LRMIN = FIND
   - LRMAX = FIND
   - NO Annihilation
   - Uses this transform -RandomCrop 32, 32 (after padding of 4) >> FlipLR>> Followed by CutOut(8, 8)
- Batch size = 512
- Target Accuracy: 90% . 

![Imgur](https://imgur.com/2sOVkUD.png)

**File Structure**
Soure code in [EvaLibrary](https://github.com/nishantb06/EvaLibrary) repository
-  models folder - Contains models_Asgmt9.py file, which has the code for the model architecture as described above 
-  main_asgmt9.py - Run this file in a colab notebook to see the results 
-  CustomResNet2.py - code for the LR range test and the overview of the entire training script
- 

# Results
### Results of LR range test
This test is performed to find out the maximum learning rate which can be used to start the training with. In the case of the One Cycle Learning rate policy it is used to determine the peak learning rate as per the algorithm.
Method - 
- Start of with a learning rate close to zero and with each iteration(400 to 500 iterations) increase the Learning rate in an exponential manner
- Plot the corresponding loss value and pick the maximum learning rate afteer which the learning rate 

![Imgur](https://imgur.com/tn2Xfy2.png)

As seen in the above image the second bottom appears at around 0.1 therefore the max learning rate is 0.1 of One cycle learning rate policy.


