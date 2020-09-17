---
layout: post
title: "Human Protein Atlas Image Classification"
date: 2020-09-17
---

Human Protein Atlas Image Classification is a competition hosted by Kaggle 2 years ago. The goal is to predict the localization of proteins, given a 4 channel stainings of the nucleus, microtubules, endoplasmic reticulum, the protein of interest. There are 28 labels in the dataset, each represent a cell organelle or subcellular structure. It is possible for the protein to reside in multiple organelles, making this a multi-label classification problem.

# Data Exploration
	
In this notebook, we will plot out some images and take a look at the data distribution  

[https://www.kaggle.com/zij212/human-protein-atlas-eda](https://www.kaggle.com/zij212/human-protein-atlas-eda)

# Data Preparation

## Train Validation Split

From the data exploration, we can see that the dataset is imbalanced. In this notebook we are going to define a function to split out the validation set, so that the proportion of each label stays close to the training set.

[https://www.kaggle.com/zij212/human-protein-atlas-train-val-split](https://www.kaggle.com/zij212/human-protein-atlas-train-val-split)

Here are some implementations of multi-label stratification that I would like to try later,   
- [Multi-label stratification](http://scikit.ml/stratification.html)
- [Multi-label stratified K-Fold](https://github.com/trent-b/iterative-stratification)


## Training set statistics

Now that we have splitted out the training data, let’s calculate the mean and standard deviation.  

[https://www.kaggle.com/zij212/human-protein-atlas-data-stats](https://www.kaggle.com/zij212/human-protein-atlas-data-stats)

# Baseline Model

The model we are going to use is Resnet34, but here are two small modifications: we need to change the input size of the first convolutional layer to 4, and the output size of the last fully connected layer to 28. The weight of the 4th channel of the 1st convolutional layer is initialized with the average of the pretrained weight of the original 3 channels of the 1st convolutional layer. During the first few epochs, we will freeze all weights of the model, except for the 1st convolutional layer and the output layer. We will then unfreeze all weights and train some more.

Some of the labels in our dataset are extremely rare, for example Rod and rings only make up 0.04% of the data. For an imbalance dataset, it is recommended to use Focal Loss, which multiplies binary cross entropy by $$(1-p)^\gamma$$ (when $$y=1$$) or $$p^\gamma$$ (when $$y=0$$). When $$\gamma=0$$, Focal Loss is the same as binary cross entropy loss, and the larger $$\gamma$$ is, the more weight we give to the False Negatives.

See my implementation at [https://www.kaggle.com/zij212/human-protein-atlas-resnet34-focalloss](https://www.kaggle.com/zij212/human-protein-atlas-resnet34-focalloss)


# Hyperparameter Tuning

Now that we have a working model, we need to find the threshold to use. In the following notebook, we went with the number that maximizes the class level f1 score.

[https://www.kaggle.com/zij212/human-protein-atlas-tune-thresholds](https://www.kaggle.com/zij212/human-protein-atlas-tune-thresholds)

# Error Analysis

# Predicting

Let’s make predictions on the testing set and submit to Kaggle.

[https://www.kaggle.com/zij212/human-protein-atlas-prediction](https://www.kaggle.com/zij212/human-protein-atlas-prediction)


