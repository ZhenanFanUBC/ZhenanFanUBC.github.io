---
title: Build Neural Networks via Julia
date: 2020-01-19 12:00:00 +0800
categories: [Machine Learning]
toc: false
seo:
  date_modified: 2020-01-23 20:37:33 -0800
---
# Build Neural Networks via Julia
In this post I want to show how to build a neural network in Julia. Julia is a great programming language for machine learning, which makes buliding neural networks much easier. 
                                                                           
[Flux](https://github.com/FluxML/Flux.jl) is a Julia machine learning package which provides layer-stacking-based interface for buliding simple neural networks such as fully connected neural network and convolutional neural network. What's more, as a differentiable language, Flux is able to take gradients of Julia code, which makes it 
easier to bulid your own more advanced models. 

In this post, I will show an example on classifying the handwritten digits in MINST dataset. Most of the material is covered in the [Flux online library](https://fluxml.ai/Flux.jl/v0.10/models/basics/).

Bellow are the packages we are going to use in this example, and I will illustrate them in detail when using them. 

````julia
using Flux, Flux.Data.MNIST, Statistics, Printf
using Flux: onehotbatch, onecold, crossentropy
using Base.Iterators: repeated
````

### Step 1: Load data
The MINST dataset is contained in *Flux.Data.MNIST* and we can load the images and labels by the following code:

````julia
# load training images
imgs = MNIST.images(:train)
# load training labels
labels = MNIST.labels(:train)
# display the sizes
display(size(imgs)), display(size(labels));
````


````
(60000,)
(60000,)
````




As we can see, the MINST dataset contains 60000 images and corresponding labels. We can randomly view some example. 

````julia
imgs[25]
````


![Output]({{ "/assets/img/post3/post3.svg" | relative_url }})

````julia
labels[25]
````


````
1
````





In order to perform training, we need to do some preprocessing on the data. Formally, we want to store the training images into a matrix $X \in \mathbb{R}^{n \times N}$, where $n$ is the image size and $N$ is the number of training data and we call the julia bulit-in functions *reshape* and *hcat* for this purpose. We also want to transform the training labels into their one-hot representations, namely we will create a matrix $Y \in \mathbb{R}^{10 \times N}$, such that each column $Y_i$ is the one-hot representation for $labels[i]$, and the *Flux* package provides a function called *onehotbatch* for that. 

````julia
# pre-processing of training images
trainX = hcat(float.(reshape.(imgs, :))...)
# pre-processing of training labels
trainY = onehotbatch(labels, 0:9)
# dispaly the size
display(size(trainX)), display(size(trainY));
````


````
(784, 60000)
(10, 60000)
````





We do the same pre-processing on validation and test data set. 
````julia
# pre-processing of validation data
validationX = hcat(float.(reshape.(MNIST.images(:validation), :))...)
validationY = onehotbatch(MNIST.labels(:validation), 0:9)
# pre-processing of test data
testX = hcat(float.(reshape.(MNIST.images(:test), :))...)
testY = onehotbatch(MNIST.labels(:test), 0:9);
````





### Step 2: Build neural network
Flux provides layer-stacking-based interface for buliding simple neural networks. In this example, we show how to build a 2-layer fully connected neural network. *Flux* package provides a function called *Dense* for constructing a fully connected layer. More specifically, we can use $Dense(n_1, n_2, \sigma)$ to build a a fully connected layer with input size $n_1$, output size $n_2$ and activation function $\sigma$. *Flux* package also provides a function called *Chain*, which allows you combine multiple layers. 

````julia
# bulid two layer neural network
# first layer with activation function relu
layer1 = Dense(784, 32, relu)
# second layer
layer2 = Dense(32, 10)
# construct the model with two layers and softmax function
model = Chain(layer1, layer2, softmax)
````


````
Chain(Dense(784, 32, relu), Dense(32, 10), softmax)
````





### Step 3: Train the neural network
To train a model we need several things:
* Loss function
* Model
* Data
* Optimizer
* Number of epochs, Stopping Criterion and Logger

##### Loss function
We use cross-entropy as the loss function: 
````julia
loss(x, y) = crossentropy(model(x), y);
````




##### Optimizer
We set the optimizer to be Adam
````julia
opt = ADAM();
````





##### Number of epochs
````julia
num_epochs = 100
data = repeated((trainX, trainY),num_epochs);
````





##### Stopping criterion and logger
````julia
# accuracy function
accuracy(x, y) = mean(onecold(model(x)) .== onecold(y)) 
# call back function
evalcb = function ()
    @printf("training loss=%f, validation accuracy=%f\n", loss(trainX, trainY), accuracy(validationX, validationY))
    accuracy(validationX, validationY) > 0.9 && Flux.stop()
end;
````





###### Start training!
````julia
# train the model 
Flux.train!(loss, params(model), data, opt, cb = evalcb)
````


````
training loss=2.274012, validation accuracy=0.114500
training loss=2.212757, validation accuracy=0.201000
training loss=2.155514, validation accuracy=0.289900
training loss=2.100332, validation accuracy=0.369900
training loss=2.045855, validation accuracy=0.426000
training loss=1.991192, validation accuracy=0.472200
training loss=1.936068, validation accuracy=0.501000
training loss=1.880539, validation accuracy=0.520200
training loss=1.824895, validation accuracy=0.535300
training loss=1.769496, validation accuracy=0.548500
training loss=1.714780, validation accuracy=0.562000
training loss=1.661142, validation accuracy=0.575000
training loss=1.608866, validation accuracy=0.588600
training loss=1.558143, validation accuracy=0.603600
training loss=1.509105, validation accuracy=0.619600
training loss=1.461786, validation accuracy=0.634200
training loss=1.416273, validation accuracy=0.648600
training loss=1.372575, validation accuracy=0.663700
training loss=1.330685, validation accuracy=0.678000
training loss=1.290625, validation accuracy=0.692600
training loss=1.252400, validation accuracy=0.706000
training loss=1.215952, validation accuracy=0.718200
training loss=1.181204, validation accuracy=0.728900
training loss=1.148056, validation accuracy=0.738600
training loss=1.116362, validation accuracy=0.747900
training loss=1.086002, validation accuracy=0.758900
training loss=1.056879, validation accuracy=0.768000
training loss=1.028928, validation accuracy=0.775800
training loss=1.002120, validation accuracy=0.782900
training loss=0.976431, validation accuracy=0.788300
training loss=0.951837, validation accuracy=0.793400
training loss=0.928310, validation accuracy=0.797300
training loss=0.905823, validation accuracy=0.801500
training loss=0.884321, validation accuracy=0.805500
training loss=0.863768, validation accuracy=0.809200
training loss=0.844120, validation accuracy=0.812100
training loss=0.825350, validation accuracy=0.816400
training loss=0.807434, validation accuracy=0.820800
training loss=0.790344, validation accuracy=0.824900
training loss=0.774048, validation accuracy=0.828800
training loss=0.758511, validation accuracy=0.832100
training loss=0.743701, validation accuracy=0.834900
training loss=0.729580, validation accuracy=0.837200
training loss=0.716109, validation accuracy=0.840700
training loss=0.703246, validation accuracy=0.842700
training loss=0.690952, validation accuracy=0.845200
training loss=0.679184, validation accuracy=0.847600
training loss=0.667905, validation accuracy=0.849300
training loss=0.657084, validation accuracy=0.850800
training loss=0.646692, validation accuracy=0.853000
training loss=0.636705, validation accuracy=0.854600
training loss=0.627104, validation accuracy=0.856300
training loss=0.617868, validation accuracy=0.858000
training loss=0.608979, validation accuracy=0.860300
training loss=0.600417, validation accuracy=0.861400
training loss=0.592167, validation accuracy=0.862600
training loss=0.584213, validation accuracy=0.863700
training loss=0.576541, validation accuracy=0.864600
training loss=0.569139, validation accuracy=0.865300
training loss=0.561991, validation accuracy=0.867000
training loss=0.555090, validation accuracy=0.868400
training loss=0.548419, validation accuracy=0.869700
training loss=0.541974, validation accuracy=0.870400
training loss=0.535748, validation accuracy=0.871600
training loss=0.529726, validation accuracy=0.872500
training loss=0.523896, validation accuracy=0.874400
training loss=0.518253, validation accuracy=0.875800
training loss=0.512788, validation accuracy=0.877200
training loss=0.507496, validation accuracy=0.878000
training loss=0.502365, validation accuracy=0.879300
training loss=0.497391, validation accuracy=0.880800
training loss=0.492567, validation accuracy=0.881600
training loss=0.487889, validation accuracy=0.882500
training loss=0.483348, validation accuracy=0.883700
training loss=0.478941, validation accuracy=0.884100
training loss=0.474661, validation accuracy=0.885200
training loss=0.470502, validation accuracy=0.886000
training loss=0.466459, validation accuracy=0.887400
training loss=0.462528, validation accuracy=0.888200
training loss=0.458703, validation accuracy=0.888400
training loss=0.454980, validation accuracy=0.889000
training loss=0.451356, validation accuracy=0.889900
training loss=0.447825, validation accuracy=0.890400
training loss=0.444386, validation accuracy=0.891200
training loss=0.441035, validation accuracy=0.891600
training loss=0.437769, validation accuracy=0.892100
training loss=0.434585, validation accuracy=0.892800
training loss=0.431479, validation accuracy=0.893500
training loss=0.428449, validation accuracy=0.893800
training loss=0.425493, validation accuracy=0.894400
training loss=0.422608, validation accuracy=0.894900
training loss=0.419792, validation accuracy=0.895900
training loss=0.417041, validation accuracy=0.896500
training loss=0.414354, validation accuracy=0.896800
training loss=0.411728, validation accuracy=0.896900
training loss=0.409160, validation accuracy=0.897100
training loss=0.406649, validation accuracy=0.897700
training loss=0.404194, validation accuracy=0.898900
training loss=0.401792, validation accuracy=0.899400
training loss=0.399442, validation accuracy=0.900000
````





### Step 4: Evaluate the result
We can evaluate the model by check the accuracy on the test data:
````julia
@show(accuracy(testX, testY));
````


````
accuracy(testX, testY) = 0.9
````


