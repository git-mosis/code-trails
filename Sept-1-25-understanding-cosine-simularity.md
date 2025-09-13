---
layout: post
title: "Understanding cosine simulairty"
permalink: /Sept-11-25-is-cosine-a-hoax/
---
## Introduction

#### Understanding Cosine Similarity in Text Analysis  

At its core, when comparing a question to an answer, RAG represents each as a vector in a multi-dimensional space and then calculates the angle between those vectors using the cosine formula. This gives a numerical measure, in terms of degrees or cosine values, of how similar the question and answer it is based off of "distance." This is done using the `cosine simularity`. 

#### What is Cosine Similarity?  

What Cosine similarity does is measure how alike two pieces of text are by converting the text into numbers and placing them on a Cartesian graph. Rather than comparing the raw words, each grouped text is represented as a vector that captures a space location in the graph. You can see this in action via the video below. 

**YouTube link here:** [3Blue1Brown
 video on Cosine Similarity](https://youtu.be/wjZofJX0v4M?si=DlnUTSL0H-IAx5pW&t=731)
[![Cosine Similarity Diagram](./cosine-simularity.png)](https://youtu.be/wjZofJX0v4M?si=DlnUTSL0H-IAx5pW&t=731)

#### Cosine Similarity Example
Instead of using learned embeddings, we will assign values manually to show how cosine similarity works. In practice, these values would normally come from an embedding model trained from text on the internet. 

- *cat* → [1, 1, 0]  
- *dog* → [1, 0.9, 0]  
- *car* → [0, 0, 1]  
- *den* → [0.9, 1, 0]  

We take the dot product and divide by the magnitudes to compute the cosine of the angle, which gives the cosine similarity score.

---

### 1) cat vs dog 
A = [1, 1, 0]  
B = [1, 0.9, 0]  

Dot product ():  
A · B = (1)(1) + (1)(0.9) + (0)(0) = 1.9  

Norms:  
‖A‖ = sqrt(1^2 + 1^2) = sqrt(2) ≈ 1.4142  
‖B‖ = sqrt(1^2 + 0.9^2) = sqrt(1.81) ≈ 1.3454  

Cosine similarity:  
cos(A, B) = 1.9 / (1.4142 * 1.3454)  
≈ 1.9 / 1.9026  
≈ 0.9986  


---

### 2) cat vs car
A = [1, 1, 0]  
C = [0, 0, 1]  

Dot product:  
A · C = 0  

Norms:  
‖A‖ = sqrt(2)  
‖C‖ = 1  

Cosine similarity:  
cos(A, C) = 0 / (sqrt(2) * 1) = 0  

---

### 3) dog vs den
B = [1, 0.9, 0]  
D = [0.9, 1, 0]  

Dot product:  
B · D = (1)(0.9) + (0.9)(1) = 0.9 + 0.9 = 1.8  

Norms:  
‖B‖ = sqrt(1^2 + 0.9^2) = sqrt(1.81) ≈ 1.3454  
‖D‖ = sqrt(0.9^2 + 1^2) = sqrt(1.81) ≈ 1.3454  

Cosine similarity:  
cos(B, D) = 1.8 / (1.3454 * 1.3454)  
= 1.8 / 1.81  
≈ 0.9945  

---

### Results
- cos(cat, dog) ≈ 0.9986  
- cos(cat, car) = 0  
- cos(dog, den) ≈ 0.9945  

You can see that the similarity between *dog* and *den* is very high, even though semantically they are not the same.  

---

### Why we need trained embeddings, Cosine Similarity alone doesn't capture much for semantic meaning

This mismatch shows a limitation: cosine similarity only reflects the angle between vectors, not the deeper context behind the words.  

To better capture semantics, pre-trained embeddings such as those from BERT or GPT are the next step.  
- **BERT** uses a masking method, dropping words and learning to predict the missing tokens.  
- **GPT** predicts the next token at each step as it reads left to right.  

When the model predicts incorrectly (e.g., guessing *dog* instead of *den*), backpropagation adjusts the vectors. If *den* consistently appears in different contexts than *dog*, their embeddings are gradually pulled apart. Thus, after the model has been trained through next-token prediction and backpropagation, when checking cosine similarity, **would we then see that den and dog are no longer have a high score in simularity**.

As a result, the embeddings capture semantic meaning, so cosine similarity between them reflects how closely words are related in context.  

---

### How does this tie into RAG?  

Why it matters is simple: **the embedding model you choose makes all the difference**. You can scale a massive vector database, but if the embeddings don’t truly reflect semantics, the retrieved answers will not be meaningful.  

