# Survival Analysis by Breast Cancer Slides

## Introduction

In medical research, histology provides important information for tumor diagnosis and treatment. Anatomic pathologists can classify disease progression by evaluating histological characteristics, such as nuclear atypia, mitotic activity, cell density, and tissue structure. 

With the advancement of gene sequencing, it is now possible to directly observe the biomarkers in the genome, such as gene expression and epigenetic modification changes, to analyze tumor symptoms. However, histological analysis is still an essential and indispensable tool for predicting the future course of patients. Organizational characteristics show the overall impact of molecular-level changes. At the same time, the organization can provide intuitive visual information to help medical staff judge the invasiveness of cancer. However, organizational analysis has significant flaws — that is, it is highly subjective and hard to repeat to obtain the same results.

In contrast, for computers, the results are derived from a specific series of computation. When the calculation process is unchanged, the same data input will have the same result.

Also, computers can extract all the information on histological imaging, and people can only observe specific areas empirically. In this way, the neglected data in histological imaging can be used by the computer to make the diagnosis more comprehensive.

Therefore, the analysis of histological imaging by computer can not only overcome the shortcomings of manual tissue analysis but also extract information that is ignored by manual tissue analysis. As a result, the application of computer analysis of histological imaging to assist medical diagnosis is very promising.

However, in most recent studies, experts are required to label the useful areas of pathological images (Region of Interest). Also, the models they present are large and complex. Consequently,  the models are not easy to use on an ordinary personal computer, and it limits their usefulness.

## Main Problems

How to build model that can provide "time-event" predictions by analyzing breast cancer histological imaging?

How to set up a fully automatic "end-to-end" batch processing system, which take .svs pathological images as input, and survival models ass output?

How to make this model be trained and used on ordinary computers?

## Solutions I applied

![Overview](https://drive.google.com/uc?id=11-PasoWDCoQIXigug4HTreoFvsHxCDpI "Overview")

In May 2017, Google Brain designed the AutoML, which can generate artificial intelligence (AI) models. NASNet is a neural network model that Google researchers use reinforcement learning and AutoML as a controller automatically trained. In 2018, NASNet was already the best model in the field of image recognition. So I choose Nasnet as the base model to built my Breast Cancer Survival Neural Network Model (SNAS) like below. 

The raw model.

![Architecture](https://raw.githubusercontent.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides/master/imgs/model.png "SNAS Architecture")

Put genome data into the model, and form the one below.

![Architecture with gene](https://raw.githubusercontent.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides/master/imgs/modelg.png "SNAS Architecture with gene")

NASNet model has two types, NASNet large model and NASNet mobile model. NASNet mobile is one of the smaller models, and its hardware requirements are smaller, but it can still achieve powerful image recognition functions. To make this model be trained and used on ordinary computers, NASNet mobile model was selected.

A [system](https://github.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides) including three parts: segmentation, extraction and training, is designed to achieve "end-to-end" batch processing. Every part does not require human intervention.

## Result
![showcase](https://raw.githubusercontent.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides/master/imgs/showcase.png "showcase")

The image above shows the classification result of NSANet classifier. In the first line are the areas classified as useful, and the areas classified as non-useful are in the second line. As you can see, the distinction is evident.

![Result](https://raw.githubusercontent.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides/master/imgs/result.png "Result")

Comparing to the survival analysis model trained on the extraction features by experts, this model can achieve similar performance. The models with the red marker are which with better performance than the base Cox PH model. 

There are 78 better than the Cox HP basic model obtained under the same conditions. From the records of the performance of 600 models in the figure above, it can be seen that the performance changes are random. Because each training is to select a region from the samples randomly, sometimes the region contains valid information, and sometimes the region does not contain valid information. Therefore, although the parameter training of each model is based on the previous model, the latter The trained model is not necessarily better than the previous one. In general, the probability that a model is better than the Cox HP basic model is 13%. From 1−(1−0.13)𝑛≤0.999, n≥50, which means training for 50 times or more,  the probability of getting a model with a predictive power better than the Cox HP basic model is higher than 99.9 %.

For one case, the randomly selected useful areas are marked in the image below. These green boxes mark them.
![overview](https://raw.githubusercontent.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides/master/imgs/overview.PNG "overview")

If we pore over these areas, we get the images below where the individual areas that are under 10X power imaging are gathered together.
![selection](https://raw.githubusercontent.com/YewtsingMu/Survival-Analysis-by-Breast-Cancer-Slides/master/imgs/selection.PNG "selection")


At the same time, the model can be fully automated trained from the .svs pathological slice images.  Also, the model is medium in size, with 7.24e+07 parameters, of which 4.26e+07 parameters are Nasnet to migrate Kaggle data for training, so the actual number of parameters to be calculated is only 2.97e+07.

