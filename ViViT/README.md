# Notes on ViViT


## 1. Video classification with ViViT

This notebook is a trial of [ViViT(Video Vision Transformer)](https://arxiv.org/abs/2103.15691) model. 

The dataset used is SynapseMNIST3D of [MedMNIST3D](https://medmnist.com/), where each data is a sequence of synapse images representing 3D volume. The data samples are displayed inside the notebook with Jupyter Widget.

### Inference 

These are some of the samples from the inference.

<p align="center">
  <img width="700" height="450" src="/ViViT/img/synapse_test_sample.gif">
</p>


## 2. Tokenization comparison in ViViT 

As embedding method of image was a breakthrough in ViT, the hypothesis is that ViViT is also effected by its embedding method on videos(or 3D vision datasets). As the original paper explained about two tokenization methods, the experiment compared the two methods given the same datasets, hyperparameters, and the other parts of the model structure. To compare the embedding structures, the experiment was done on 6 MNIST 3D datasets and tested on 3 learning rates. No pretraining was done and the result was logged at TensorBoard. 

Two tokenization methods are Uniform Frame Sampling and Tubelet Embedding. Uniform Frame Sampling namely samples 2D images and concatenates them in accordance with time. Thus there can be omitted or overlapped information in the input embedding. On the other hand, Tubelet Embedding embeds 3D video itself using 3D convolution, which means that the information is neither omitted nor overlapped. Also, Tubelet Embedding reflects spatio-temporal relation of the video frames by embedding local frames time-linearly. 

### The Result

On Nodule MNIST 3D and Organ MNIST 3D datasets, Tubelet embedding excelled in accuracy and loss in validation.  

<p align="center">
  <img width="500" height="300" src="./ViViT/img/vivit-result-nodule.png">
</p>

<p align="center">
  <img width="500" height="300" src="./ViViT/img/vivit-result-organ.png">
</p>

The difference of training time between two methods was not significant or valid according to datasets and learning rates.

<p align="center">
  <img width="550" height="100" src="./ViViT/img/vivit-train-time.png">
</p>

On Adrenal MNIST 3D dataset under the same learning rate, validation result for Uniform Frame Sampling converged whereas for Tubelet Embedding diverged. Thus the two methods for tokenization demands different hyperparameters for training and (probably) model.  

<p align="center">
  <img width="500" height="300" src="./ViViT/img/vivit-result-adrenal.png">
</p>

In sum, the experiment showed that Tubelet Embedding tends to acquire better accuracy and lower loss than Uniform Frame Sampling given same model, hyperparameter, and training time. Note that the experiment was done on small datasets with not pre-trained, small Transformer. 

Overall interactive result can be viewed on [Tensorboard.dev](https://tensorboard.dev/experiment/PKs2SEeNQLO68B4tB7U8tg).


## 3. ViViT with/without Token Learner

This notebook explores the effect of [**Token Learner**](https://proceedings.neurips.cc/paper/2021/file/6a30e32e56fce5cf381895dfe6ca7b6f-Paper.pdf) in ViViT. 

The datasets are from [MedMNIST 3D](https://medmnist.com/), which contains medical 3D images with different types of classes. The model structure was tested on patch size 8 and 16, and token learner was put in the middle (half point of the transformer blocks). AdamW optimization method was used for regulralization and the learning rate was reduced on plateau.

### The Result

<p align="center">
  <img width="500" height="200" src="./ViViT/img/vivit_tl_nodulemnist3d.png">
</p>

<p align="center">
  <img width="500" height="200" src="./ViViT/img/vivit_tl_organmnist3d.png">
</p>

The overall performance of the model with token learner was better than the naive model in validation accracy and loss over epochs. Also, there was no signs of overfitting with token learner even though the training time was shortened. The result shows that with token learners models learn faster, without significant risk of overfitting.

All of the result graphs are displayed on [TensorBoard.dev](https://tensorboard.dev/experiment/nYVP58K4Q1GEuWLbkWBFow/). 

