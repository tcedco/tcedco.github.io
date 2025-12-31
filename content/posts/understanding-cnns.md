---
title: Understanding Convolutional Neural Networks (CNNs)
layout: post
outputs: [html, json]
categories: [machine learning, deep learning, neural networks]
date: 2024-09-14
math: true
---

If you’ve ever wondered how machines are able to “see” things in images or videos, then Convolutional Neural Networks (CNNs) are one of the key technologies behind that magic. CNNs have become the backbone of many modern computer vision tasks, from identifying objects in images to powering facial recognition software. In this Codects installment, I’ll explain the inner workings of CNNs, their structure, how they process images, and the mathematics behind them.

This post is going to be longer and more technical, but don’t worry—I’ll break it down step by step.

# What Are Convolutional Neural Networks (CNNs)?

At its core, a Convolutional Neural Network (CNN) is a type of deep learning model designed specifically for processing structured grid data, such as images. CNNs have a remarkable ability to capture spatial hierarchies in images, allowing them to detect patterns, edges, textures, and more, regardless of where these patterns appear in the image.

You can think of CNNs as a series of filters that process input images layer by layer, extracting increasingly complex features at each step. At the end of the process, the network is able to make predictions, such as recognizing whether there’s a dog, a cat, or any other object in the image.

# How CNNs Differ from Traditional Neural Networks
Before diving into CNNs, it’s worth understanding why we don’t simply use traditional neural networks for image processing. Fully connected networks (also known as multilayer perceptrons) are the default approach for many tasks. But for images, they come with a few major limitations:

- **Input Size**: Images, especially high-resolution ones, have many pixels. For example, a 224x224 RGB image has over 150,000 input values. Feeding that into a fully connected network would require enormous computational power.
- **Spatial Relationships**: Traditional networks don’t take the spatial relationship between pixels into account. They treat every pixel as independent of its neighbors, which isn’t ideal for images where nearby pixels often share useful information (like edges and textures).

CNNs address these problems by exploiting local connections between pixels and reducing the number of parameters through convolutions and pooling operations.

# The Structure of a CNN

The architecture of a CNN can be broken down into several key layers:

1. Convolutional Layers
2. Pooling (Subsampling) Layers
3. Fully Connected Layers
4. Activation Functions (such as ReLU)

Let’s explore each of these in detail.

## 1. Convolutional Layers

The convolutional layer is the key building block of CNNs. In this layer, the network applies a set of filters (also called kernels) to the input image to detect specific features like edges, corners, or textures. The idea is simple: rather than look at the entire image at once, the filter slides over small patches of the image, performing a dot product between the filter and the patch of pixels it covers.

### How Does Convolution Work?

Mathematically, convolution is a sliding dot product. Suppose you have a filter (or kernel) \\(K\\) and an input image \\(I\\). The filter moves across the image from left to right, top to bottom, computing the sum of element-wise products at each position.

Here’s what the convolution operation looks like:

$$
S(i, j) = (I * K)(i, j) = \sum_m \sum_n I(i+m, j+n) K(m, n)
$$

Where  \\(S(i, j)\\) is the output at position \\((i, j)\\), \\(I(i+m, j+n)\\) is the pixel value of the input image, and \\(K(m, n)\\) is the filter applied at that position

The result is a **feature map**, a matrix showing how strongly certain patterns or features match across the image.

### Visualizing Convolution

To better understand how convolution works, imagine you have a small 3x3 filter like this:

$$
K = \begin{bmatrix}
1 & 0 & -1 \\\\
1 & 0 & -1 \\\\
1 & 0 & -1
\end{bmatrix}
$$

In this case, the filter is designed to detect vertical edges. When you slide this filter over an image, it will produce a feature map highlighting vertical edges in the image. This is just a simple example; in practice, filters can be more complex and capture a wide range of features.

## 2. Pooling Layers

Once the convolutional layer has extracted feature maps, the next step is to **downsample** the data. This is when pooling layers come into play. Pooling reduces the spatial dimensions of the feature maps, making the network more computationally efficient and less sensitive to small changes in the input.

The most common form of pooling is **max pooling**, where the network selects the maximum value from a small region of the feature map. This helps the network focus on the most important features while discarding irrelevant information. Often times the size of the pooling region is 2x2, and the stride (the amount the pooling region moves) is also 2.

$$
\text{Max Pooling Example} \rightarrow
\begin{bmatrix}
1 & 3 & 2 & 4 \\\\
5 & 6 & 7 & 8 \\\\
3 & 2 & 1 & 0 \\\\
9 & 6 & 4 & 2
\end{bmatrix}
\rightarrow
\begin{bmatrix}
6 & 8 \\\\
9 & 4
\end{bmatrix}
$$

Here, the max pooling operation selects the maximum value from each 2x2 region, resulting in a smaller feature map. This process is repeated for each feature map in the network.

## 3. Fully Connected Layers

After the convolutional and pooling layers, the network typically ends with one or more fully connected layers. These layers are similar to those in traditional neural networks, where every neuron is connected to every neuron in the previous layer. The fully connected layers process the high-level features extracted by the convolutional layers and make the final predictions.

In most image classification tasks, the fully connected layers are followed by a softmax activation function to produce class probabilities. For example, if the task is to identify whenever an image contains a cat, dog, or bird, the network will output three probabilities that sums to 1.

## 4. Activation Functions

Activation functions introduce non-linearity into the network, allowing it to learn complex patterns in the data. The most common activation function used in CNNs is the Rectified Linear Unit (ReLU), which replaces all negative values in the feature map with zero.

$$
\text{ReLU}(x) = \max(0, x)
$$

ReLU is computationally efficient and helps the network converge faster during training.

## The Mathematics Behind CNNs

At the heart of CNNs are linear algebra operations like matrix multiplication and convolution. These operations are used to compute the output of each layer, apply activation functions, and update the network’s weights during training. However it's important to remember that the network is learning **fiters** on its own during training through a process called **backpropagation** and **gradient descent**.

A few important concepts to understand are:

- **Weight Sharing**: In CNNs, the same filter is applied to every patch of the image. This is known as weight sharing, and it helps the network learn translation-invariant features.
- **Strides and Padding**: Strides determine how much the filter moves across the image, while padding adds zeros around the image to preserve its size.
- **Learning Fiters**: CNNs don’t rely on predefined filters. Instead, during training, the network learns the optimal filters for detecting features like edges, textures, or more complex patterns.