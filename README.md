# Passphrase-Hackers

This implementation was done as tenure long project under the **[Brain and Cognitive Science Club, IITK](https://bcs-iitk.github.io/)** by the following 2nd year UG Secretaries:

1. [Udbhav Agarwal](https://github.com/udbhav-44)
2. [Sagar Arora](https://github.com/qu-bit1)

Mentors:

1. [Aniruddh Pramod](https://github.com/atryt0ne)

Trained Model weights trained on 700 epochs also provided in the repo under the name `700_passphrase.pth`

## Introduction

This repository is an implementation of the paper _[A Practical Deep Learning-Based Acoustic Side Channel Attack on Keyboards](https://arxiv.org/abs/2308.01074)_

With recent developments in deep learning, the ubiquity of microphones and the rise in online services via personal devices, acoustic side channel attacks(ASCA) present a greater threat to keyboards than ever. This repo presents a practical implementation of a state-of-the-art deep learning model in order to classify laptop keystrokes, using a smartphone integrated microphone.
</br>

## Pipeline

1. KeyStroke Isolation
2. Setting Threshold
3. Time Shift Augmentation (Augmentation not implemented)
4. Generating Mel-Spectrograms
5. Masking of the Spectrograms (Augmentation Techinique not used)
6. Implementing and Training the Convolution X Attention Model (CoAtNet)
7. Result Analysis

Note : The code provided here does not contain the implement data augmenation techniques on the Dataset

![Data Processing Pipeline](<./Images/Screenshot 2024-03-01 at 1.45.59 PM.png>)

## About the Dataset

36 of the laptop’s keys were used (0-9, a-z) with each being pressed 25 times in a row, varying in pressure and finger, and a single file containing all 25 presses.

The recordings were done thorough 2 sources

1. SmartPhone Microphone
2. Zoom Meeting

## Methodology :

### 1. **KeyStroke Isolation:**

Implement a function to extract individual keystroke
![Key Stroke Isolation](<./Images/Screenshot 2024-03-01 at 1.46.46 PM.png>)

Performing the Fast Fourier transform(FFT) on the recording and summing
the coefficients across frequencies to get ‘energy’.
An energy threshold is then defined and used to signify the presence of a keystroke.

### 2. Feature Extraction:

-   methods for FE $\to$ FFT, MFCC, XC (not used because MFCC - human speech, FFT - linear scale which is bad)
-   paper used mel-spectrograms
-   mel-spectograms $\to$ method of depicting sound waves and is a modified version of a spectrogram, unit of freq - mel
-   spectograms $\to$ represents sound as a map of coloured pixel(brightness - amplitude of frequency), Y - freq, X - time

![Feature Extraction](<./Images/Screenshot 2024-03-01 at 1.47.30 PM.png>)

### 3. Model Selection:

CoAtNet can be seen to consist of two depth-wise convolutional layers followed by two global relative attention layers. The act of combining convolution and self-attention methods allows for rapid processing of patterns in the data while down-sampling the size (convolution) before determining the relevance of these patterns to one another through the calculation of attention scores (self-attention)

### 4. Data Augmentation:

-   Prior to feature extraction, signals were time-shifted randomly by up to 40% in either direction
-   The mel-spectrograms were then generated using 64 mel bands, a window length of 1024 samples and hop length of 500
-   Using the spectrograms, a second method of data augmentation was implemented called masking.
-   This method involves taking a random 10% of both the time and frequency axis and setting all values within those ranges to the mean of the spectrogram, essentially ‘blocking out’ a portion of the image.
-   Using time warping and spectrogram masking combined is called SpecAugment and was found to encourage the model to generalise and avoid overfitting the training data

## 5. HyperParameters:

We trained the model on the 700 epochs
![HyperParameter Tuning](<./Images/Screenshot 2024-03-01 at 1.48.34 PM.png>)

## Citations

```
@inproceedings{Harrison_2023,
   title={A Practical Deep Learning-Based Acoustic Side Channel Attack on Keyboards},
   url={http://dx.doi.org/10.1109/EuroSPW59978.2023.00034},
   DOI={10.1109/eurospw59978.2023.00034},
   booktitle={2023 IEEE European Symposium on Security and Privacy Workshops (EuroS&amp;PW)},
   publisher={IEEE},
   author={Harrison, Joshua and Toreini, Ehsan and Mehrnezhad, Maryam},
   year={2023},
   month=jul }
```
