---
layout: post
title: Simple and Scalable Predictive Uncertainty Estimation using Deep Ensembles
---
 
## View of the world and the Challenge:
Technology has become inextricably intertwined with people’s lives and businesses. 
By predicting everything from stock price to behaviours, learning algorithms coupled with voluminous data have permeated every industry, for revenue enhancement and/or cost-cutting.
But the world is sparse, signals are like grains in heaps of chaff. 
To make sense of these sparse signals and derive insights, creating compressed representations of real-world data is imperative.

## Solution according to me and its Impact:
I recently stumbled upon VAEs which can be used to solve the aforesaid problem. 
Variational AutoEncoders(VAEs) are an unsupervised deep learning algorithm which learns a dense representation of data by reconstruction. The data is passed through an encoder which creates a low dimensional latent vector; which is then passed to a decoder to reconstruct the original data. The latent vector is composed of the high-level features deemed important by the algorithm for reconstructing the original data and hence it can be trained to learn the compressed representation in an unsupervised manner, that is, without using any inference labels.
These compressed representations of data can be highly useful in any problem where a sparse input needs to be processed for further downstream tasks. For example, a recent paper based on defending Convolutional Neural Networks (CNN's) from adversarial attacks (adding Gaussian noise to the image to fool the algorithm); found state-of-the-art accuracy in using VAEs for prior denoising of the images before passing to CNN's. This was accomplished by training the algorithm on artificially corrupted images, helping it learn the features essential for reconstructing the clean original image.

## Case Study and Learnings:
Similarly, VAEs can also be used for anomaly detection. Anomaly detection is traditionally accomplished by supervised methods, that is, by detecting patterns in the data. 
Unsupervised anomaly detection using VAEs is therefore extremely useful, especially in time-series financial data. 
In one of my personal projects, by training a VAE on Bitcoin price data, I was able to generate a dense representation of 'normal' price data (using indicators such as moving and exponential averages). Then by measuring the reconstruction error of the test data points, it can be inferred that if the error is high; then it is probably an anomaly and vice versa.
 
## Practicality:
This is just a start; I think the model can be made more adaptive, predictive and hence commercially viable. This algorithm, like traditional machine learning algorithms, can run on a rolling basis on new data; through a sliding window approach for continual improvement.
Thus, I believe that VAEs can be trained to solve tasks such as unsupervised signal reconstruction and anomaly detection to help analysis and prediction.
