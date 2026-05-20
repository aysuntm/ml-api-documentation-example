# Sentiment Analysis

## Introduction

This model performs a sentiment analysis using a binary classification model. The model classifies the text into two opposite sentiment categoriees: positive (1) or negative (0).

## Model Architecture

The following diagram shows the model architecture. The sentiment classification model consists of a tokenizer that processes raw text and converts it into tokens, which are smaller, structured units of input. These tokens are passed into a 300-dimensional embedding layer that maps each token to a dense vector representation.

The embedded representations are then fed into a Bidirectional Long Short-Term Memory (BiLSTM) layer with 256 hidden units to capture contextual information from both forward and backward directions. The resulting outputs are aggregated using a max pooling layer to reduce dimensionality while preserving the most salient features.

The pooled representation is passed to a fully connected (dense) layer with 128 neurons. A Rectified Linear Unit (ReLU) activation function is applied to introduce non-linearity. Finally, a two-node output layer with a softmax activation function produces a probability distribution over the two target classes, representing the likelihood of positive versus negative sentiment.

### Architecture Diagram
```
+-------------+
| Input Text  |
+-------------+
      ↓
+-------------+
|  Tokenizer  |
+-------------+
      ↓
+-------------+
|  Embedding  |
|  (300-dim)  |
+-------------+
      ↓
+-------------+
| BiLSTM (256)|
+-------------+
      ↓
+-------------+
| Max Pooling |
+-------------+
      ↓
+-------------+
| Dense (128) |
|    ReLU     |
+-------------+
      ↓
+-------------+
| Softmax (2) |
+-------------+
```

## Dataset Schema

The sentiment analysis model expects a structured dataset containing raw text and corresponding metadata. 

Below is the schema definition for the input data:

| Field Name | Type | Required | Description | Example Value |
| :--- | :--- | :---: | :--- | :--- |
| `review_text` | String | Yes | Raw customer review text used as input for the tokenizer. | `"The product arrived on time and works flawlessly!"` |
| `label` | Integer | Yes | Sentiment target label (`0` = negative, `1` = positive). | `1` |
| `source` | String | No | Identifier tag indicating the origin of the data. | `"amazon_reviews_v2"` |
| `timestamp` | String (ISO) | No | UTC timestamp indicating when the review was collected. | `"2026-05-20T12:00:00Z"` |

## Training Details

The model was trained using the following hyperparameter configuration:

| Hyperparameter | Value | Description |
| :--- | :--- | :--- |
| **Learning rate** | `3e-4` | Step size used to update model weights during optimization. |
| **Batch size** | `64` | Number of training samples processed before each parameter update. |
| **Max epochs** | `10` | Total number of complete passes through the training dataset. |
| **Dropout** | `0.3` | Regularization technique that randomly deactivates 30% of neurons during training to reduce overfitting. |
| **Optimizer** | `AdamW` (weight decay = `0.01`) | Optimization algorithm combining Adam with decoupled weight decay for improved generalization. |
| **Scheduler** | Cosine decay + `1k` warmup steps | Learning rate schedule that gradually warms up training for 1,000 steps, then decays following a cosine function. |
| **Seed** | `42` | Random seed used to ensure reproducibility of training results. |
