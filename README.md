# CGANCelebA
Conditional GAN project for the Deep Learning course of 2019 held by professor Elisa RIcci, done by Filippo Bordon, Olivier Nicolini and me.
Our objective in this project was to design a GAN able to produce images of faces conditioned with different labels. More specifically we wanted to take an already existing architecture, modify it and obtain better results generating faces conditioned by some specifics attributes.

# Dataset
For our project we used CelebA, a widely used dataset for faces, that contains more than 200k faces of famous people. The images used are cropped on the faces, and re-sized at 64x64 pixels. Each one of these images has 40 binary labels.

# Starting architecture
As starting architecture a modified version of the DCGAN that uses one label in the one-hot encoding was used.
The generator takes as input the noise vector and a vector of two elements where the label is encoded in one-hot fashion.
Both inputs are deconvoluted separately, concatenated and then deconvoluted together until a 64x64 image is obtained.
![alt text](https://github.com/SZamboni/CGANCelebA/blob/master/images/BaseArchG.png)

The discriminator similarly takes as input an image and the label, which is encoded as a 64x64 image with two channels.
Each channel contains only ones or zeros depending on the label: for example 01 for male and 10 for female, in a one-hot fashion. Again, these inputs are convoluted independently, then concatenated and convoluted until we have a single number: the probability of the image being real.
![alt text](https://github.com/SZamboni/CGANCelebA/blob/master/images/BaseArchD.png)

With this architecture generated images were blurry, contours were not well defined and worked only with very discriminative labels like male/female. We tried to modify it in order to use two labels instead of one, increasing the dimension of the label channels from 2 to 4, but the network did not converge and the output was only noise, as you can see in the following picture using the label Male(on the bottom four rows)/Female(on the top four rows), obtained after 10 epochs of training on Google Colab (around 7 hours of training):

<img src="https://github.com/SZamboni/CGANCelebA/blob/master/images/baseres.png" width="524">

# Our Architecture

# Spectral Normalization
In order to increase the quality of the images and the stability of the training we introduced spectral normalization in the discriminator.
Using the base architecture with the one-hot encoding on multiple labels with the addition of the spectral normalization in the discriminator we were able to obtain images more defined. Faces were better defined, but the network was not able to distinguish properly between labels: we needed a way for the network to differentiate more between labels.

# Categorical Batch Normalization
Categorical batch normalization is a type of batch normalization that changes the normalization parameters based on the input category. It is applied to the generator and for every combination of labels we assign a different category; for example the generation of females with black hair and of females with non black hair will use different batch normalization parameters.This approach has the advantage that the network differentiate more between the various labels but it also adds more parameters to compute, slowing down the training.
Using the base architecture with the one-hot encoding on multiple labels and the conditional batch normalization we obtained results respecting more the labels, but images were not as defined as when using spectral normalization, but these approaches can be combined.

# Final Architecture
Our final architecture is the base architecture modified to use more than one label both in the generator and in the discriminator using the one-hot encoding, with some additional features:
* More convolutions of the label in the generator
* Categorical batch normalization in the generator
* Spectral normalization in the discriminator.
This architecture has all the advantages we were looking for: it is more stable (in a sense that the loss of the discriminator evaluating the generated images is more or less constant), it is able to produce good quality faces with well defined traits and is able to correctly differentiate between labels.

Final results after 10 epochs of training, in the first two rows there should be female with non black hair, then female with black hair, then male with non black hair and finally male with black hair.

<img src="https://github.com/SZamboni/CGANCelebA/blob/master/images/FinalM-F-black.png " width="524">

Final results after 10 epochs of training, in the first two rows there should be female non smiling, then female smiling, then male non smiling and finally male smiling:

<img src="https://github.com/SZamboni/CGANCelebA/blob/master/images/FinalM-F-smile.png " width="524">
