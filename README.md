# Table of Contents

1. [EDA](#eda) <br>
    1. [Viewing Images](#view) <br>
    2. [Class Distribution](#class) <br>
2. [Modeling](#modeling) <br>
    1. [Tuning One Parameter](#one) <br>
    2. [Complex Models](#complex) <br>
    3. [Final Model](#final) <br>
3. [Results](#results)

# EDA <a id='eda'></a>

## Viewing Images <a id='view'></a>

In this project, we are working with images of chest X-rays which we are classifying into two categories: pneumonia or normal. First, let's look at the difference between these.

#### Normal:
<table style="border-collapse: collapse;">
    <tr>
        <td style="border: none;"><img src="Images/normal1.jpeg" alt="normal1" width="450px"/></td>
        <td style="border: none;"><img src="Images/normal2.jpeg" alt="normal2" width="400px"/></td>
        <td style="border: none;"><img src="Images/normal3.jpeg" alt="normal3" width="400px"/></td>
    </tr>
</table>

#### Pneumonia:
<table style="border-collapse: collapse;">
    <tr>
        <td style="border: none;"><img src="Images/pneumonia1.jpeg" alt="pneumonia1" width="450px"/></td>
        <td style="border: none;"><img src="Images/pneumonia2.jpeg" alt="pneumonia2" width="400px"/></td>
        <td style="border: none;"><img src="Images/pneumonia3.jpeg" alt="pneumonia3" width="400px"/></td>
    </tr>
</table>

From <a href="https://www.radiologyinfo.org/en/info.cfm?pg=pneumonia">Radiology.org</a>: <br> _Pneumonia is an infection that causes inflammation in one or both of the lungs. It can be caused by a virus, bacteria, fungi or other germs. The infection is usually acquired when a person breathes in air carrying germs._

When radiologists look at these images, they look for white spots called infiltrates that identify an infection, as well as abscesses or fluids that could be caused by pneumonia. We find it a bit difficult to tell the difference between these due to not being trained radiologists, but we can definitely say some x-rays (such as the leftmost pneumonia picture above) look more "cloudy" than others.

Further reviewing our data, we can see that:
* Every image is grayscale, only containing color data in the gray channel (as opposed to RGB alpha channels).
* Each image has a different pixel dimension, many with non-square aspect ratios. 
* Most images have tick marks on the left and right edges.
* Many images contain other text near the edges.
* Some images show the patient rotated at an angle.

<img src="Images/tp_512_prob.png" alt="image_problems" width="450px"/>

To remedy any issues caused by this, we will change parameters of ImageDataGenerator while creating our models.

## Class Distribution <a id='class'></a>

Before we start modeling, we want to check for class imbalance. 

<img src="Images/class_distribution.png" alt="image_problems" width="450px"/>

We can see we have a 27:73 split. This isn't a huge imbalance, but definitely something to keep in mind when evaluating our models, as our null accuracy is 73%. This is the accuracy we could achieve by always predicting the most frequent class.

# Modeling <a id='modeling'></a>

## Tuning One Parameter <a id='one'></a>
First, we experiment to see how changing parameters in our data and models affects our results. To see this process, go to the “Tuning_One_Parameter” jupyter notebook. 

Below are the parameters we found to give us better results:
* Early Stopping with a higher patience
* Additional layers
* Dropout before flatten layer
* Image Transformations (Zoom, Shear, and Shift)
* Larger Image

## Complex Models <a id='complex'></a>

To find our best model, we used a mix of these best parameters. This process is in the "Complex_Models" jupyter notebook.

Our best model had the following parameters:
* 6x6 and 5x5 layer
    * Helps us extract more features and patterns
* 128x128 images
    * The largest images we could give our model with our current hardware 
* Quad filters
    * 
* Dropout before flatten
    * Helps avoid overfitting
* Shift, Shear and Zoom on training set
    * Zoom removes the majority of ticks and edges we previously mentioned were on some pictures
    * Shear accounts for rotation of patients in some X-rays
    * Shift accounts for non-centered images
    * Helps us avoid overfitting to our training set's images


# Results <a id='results'></a>

Let's look at the results of our best model in detail below.

**Accuracy**: Train - 0.938, Test - 0.872 <br>
**Loss**: Train - 0.158, Test - 0.321 
 
These were the best accuracy and loss results for our test set out of all the models we ran.
<div style="display: inline-block">
<img src="Images/loss_acc.png" alt="loss and accuracy graph" width="400px" align="left">
<p style="margin-top:13%">
We can see here that the validation set loss and accuracy follows the same improving trend as that of the training set, meaning we have a good fit on our data.
</p>
</div>

<div style="display: inline-block; width: 100%">
<img src="Images/conf_matrix.png" alt="confusion matrix" width="400px" align="left">
<p style="margin-top:13%">
    <strong>Precision</strong>: Normal - 0.85, Pneumonia - 0.88 <br>
    <strong>Recall</strong>: Normal - 0.80, Pneumonia - 0.92 <br>
    <strong>F1 Score</strong>: Normal - 0.82, Pneumonia - 0.90    
</p>    
</div>

The confusion matrix shows us that we have a low amount of false negatives/positives. We are able to keep recall above 0.90 while still have a high precision, giving us a high f1 score. Since this model is making predictions on people's medical diagnosis, we need to be sure to prioritize recall. We'd rather someone healthy get checked for pneumonia than someone sick be misdiagnosed as healthy.

Let's take a look at some examples of images classified by our model.

[stuff from classification notebook]

Overall, our model gives us good results!