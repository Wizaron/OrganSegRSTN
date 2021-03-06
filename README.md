# OrganSegRSTN: an end-to-end coarse-to-fine organ segmentation framework
version 1.0 - Apr 20 2018 - by Qihang Yu, Yuyin Zhou and Lingxi Xie

#### **Qihang Yu and Yuyin Zhou are the main contributors to this repository.**

Yuyin Zhou implemented [the original coarse-to-fine framework](https://github.com/198808xc/OrganSegC2F),
Qihang Yu improved it to allow end-to-end training, and Lingxi Xie later wrapped up these codes for release.

#### If you use our codes, please cite our paper accordingly:

  **Qihang Yu**, Lingxi Xie, Yan Wang, Yuyin Zhou, Elliot K. Fishman, Alan L. Yuille,
    "Recurrent Saliency Transformation Network: Incorporating Multi-Stage Visual Cues for Small Organ Segmentation",
    in IEEE Conference on CVPR, Salt Lake City, Utah, USA, 2018.

https://arxiv.org/abs/1709.04518

###### and possibly, our previous work (the basis of this work):

  **Yuyin Zhou**, Lingxi Xie, Wei Shen, Yan Wang, Elliot K. Fishman, Alan L. Yuille,
    "A Fixed-Point Model for Pancreas Segmentation in Abdominal CT Scans",
    in International Conference on MICCAI, Quebec City, Quebec, Canada, 2017.

https://arxiv.org/abs/1612.08230

All the materials released in this library can **ONLY** be used for **RESEARCH** purposes.

The authors and their institution (JHU/JHMI) preserve the copyright and all legal rights of these codes.

**Before you start, please note that there is a LAZY MODE,
  which allows you to run the entire framework with ONE click.
  Check the contents before Section 4.3 for details.**


## 1. Introduction

OrganSegRSTN is a code package for our paper:

  **Qihang Yu**, Lingxi Xie, Yan Wang, Yuyin Zhou, Elliot K. Fishman, Alan L. Yuille,
    "Recurrent Saliency Transformation Network: Incorporating Multi-Stage Visual Cues for Small Organ Segmentation",
    in IEEE Conference on CVPR, Salt Lake City, Utah, USA, 2018.

OrganSegRSTN is a segmentation framework designed for 3D volumes.
    It was originally designed for segmenting abdominal organs in CT scans,
    but we believe that it can also be used for other purposes,
    such as brain tissue segmentation in fMRI-scanned images.

OrganSegRSTN is based on the state-of-the-art deep learning techniques.
    This code package is to be used with CAFFE, a deep learning library.
    We make use of the python interface of CAFFE, named pyCAFFE.

It is highly recommended to use one or more modern GPUs for computation.
    Using CPUs will take at least 50x more time in computation.

**We provide an easy implementation in which the training stages has only 1 fine-scaled iteration.
  If you hope to add more, please modify the prototxt file accordingly.
  As we said in the paper, our strategy of using 1 stage in training and multiple iterations in testing works very well.**


## 2. File List

| Folder/File                 | Description                                          |
|:--------------------------- |:---------------------------------------------------- |
| `README.md`                 | the README file                                      |
|                             |                                                      |
| **DATA2NPY/**               | codes to transfer the NIH dataset into NPY format    |
| `dicom2npy.py`              | transferring image data (DICOM) into NPY format      |
| `nii2npy.py`                | transferring label data (NII) into NPY format        |
|                             |                                                      |
| **DiceLossLayer/**          | CPU implementation of the Dice loss layer            |
| `dice_loss_layer.hpp`       | the header file                                      |
| `dice_loss_layer.cpp`       | the CPU implementation                               |
|                             |                                                      |
| **OrganSegRSTN/**           | primary codes of OrganSegRSTN                        |
| `coarse2fine_testing.py`    | the coarse-to-fine testing process                   |
| `coarse_fusion.py`          | the coarse-scaled fusion process                     |
| `coarse_testing.py`         | the coarse-scaled testing process                    |
| `Crop.py`                   | the crop layer (cropping a region from the image)    |
| `Data.py`                   | the data layer                                       |
| `indiv_training.py`         | training the coarse and fine stages individually     |
| `init.py`                   | the initialization functions                         |
| `joint_training.py`         | training the coarse and fine stages jointly          |
| `Uncrop.py`                 | the uncrop layer (putting the regional output back)  |
| `oracle_fusion.py`          | the fusion process with oracle information           |
| `oracle_testing.py`         | the testing process with oracle information          |
| `run.sh`                    | the main program to be called in bash shell          |
| `surgery.py`                | the surgery function                                 |
| `utils.py`                  | the common functions                                 |
|                             |                                                      |
| **OrganSegRSTN/prototxts/** | prototxt files of OrganSegRSTN                       |
| `deploy_C3.prototxt`        | the prototxt file for coarse-scaled testing          |
| `deploy_F3.prototxt`        | the prototxt file for fine-scaled testing            |
| `deploy_O3.prototxt`        | the prototxt file for oracle testing                 |
| `training_I3x1.prototxt`    | the prototxt file for individual training (1xLR)     |
| `training_I3x10.prototxt`   | the prototxt file for individual training (10xLR)    |
| `training_J3x1.prototxt`    | the prototxt file for joint training (1xLR)          |
| `training_J3x10.prototxt`   | the prototxt file for joint training (10xLR)         |
| `training_S3x1.prototxt`    | the prototxt file for separate training (1xLR)       |
| `training_S3x10.prototxt`   | the prototxt file for separate training (10xLR)      |
|                             |                                                      |
| **logs/**                   | training log files on the NIH dataset                |


The multiplier (1 or 10) applies to all the trainable layers in the fine stage of the framework.


## 3. Installation


#### 3.1 Prerequisites

###### 3.1.1 Please make sure that your computer is equipped with modern GPUs that support CUDA.
    Without them, you will need 50x more time in both training and testing stages.

###### 3.1.2 Please also make sure that python (we are using 2.7) is installed.


#### 3.2 CAFFE and pyCAFFE

###### 3.2.1 Download a CAFFE library from http://caffe.berkeleyvision.org/ .
    Suppose your CAFFE root directory is $CAFFE_PATH.

###### 3.2.2 Place the files of Dice loss layer at the correct position.
    dice_loss_layer.hpp -> $CAFFE_PATH/include/caffe/layers/
    dice_loss_layer.cpp -> $CAFFE_PATH/src/caffe/layers/

###### 3.2.3 Make CAFFE and pyCAFFE.


## 4. Usage

Please follow these steps to reproduce our results on the NIH pancreas segmentation dataset.

**NOTE**: Here we only provide basic steps to run our codes on the NIH dataset.
    For more detailed analysis and empirical guidelines for parameter setting
    (this is very important especially when you are using our codes on other datasets),
    please refer to our technical report (check our webpage for updates).


#### 4.1 Data preparation

###### 4.1.1 Download NIH data from https://wiki.cancerimagingarchive.net/display/Public/Pancreas-CT .
    You should be able to download image and label data individually.
    Suppose your data directory is $RAW_PATH:
        The image data are organized as $RAW_PATH/DOI/PANCREAS_00XX/A_LONG_CODE/A_LONG_CODE/ .
        The label data are organized as $RAW_PATH/TCIA_pancreas_labels-TIMESTAMP/label00XX.nii.gz .

###### 4.1.2 Use our codes to transfer these data into NPY format.
    Put dicom2npy.py under $RAW_PATH, and run: python dicom2npy.py .
        The transferred data should be put under $RAW_PATH/images/
    Put nii2npy.py under $RAW_PATH, and run: python nii2npy.py .
        The transferred data should be put under $RAW_PATH/labels/

###### 4.1.3 Suppose your directory to store experimental data is `$DATA_PATH`:
    Put $CAFFE_PATH under $DATA_PATH/libs/
    Put images/ under $DATA_PATH/
    Put labels/ under $DATA_PATH/
    Download the scratch model below and put it under $DATA_PATH/models/pretrained/

[The scratch model](https://drive.google.com/open?id=1C7XPat4BhAHPA3azIssmc3cd7zJOmQ4k) - see the explanations in 4.2.3.

NOTE: If you use other path(s), please modify the variable(s) in run.sh accordingly.


#### 4.2 Initialization (requires: 4.1)

###### 4.2.1 Check `run.sh` and set $DATA_PATH accordingly.

###### 4.2.2 Set `$ENABLE_INITIALIZATION=1` and run this script.
    Several folders will be created under $DATA_PATH:
        $DATA_PATH/images_X|Y|Z/: the sliced image data (data are sliced for faster I/O).
        $DATA_PATH/labels_X|Y|Z/: the sliced label data (data are sliced for faster I/O).
        $DATA_PATH/lists/: used for storing training, testing and slice lists.
        $DATA_PATH/logs/: used for storing log files during the training process.
        $DATA_PATH/models/: used for storing models (snapshots) during the training process.
        $DATA_PATH/prototxts/: used for storing prototxts (called by training and testing nets).
        $DATA_PATH/results/: used for storing testing results (volumes and text results).
    According to the I/O speed of your hard drive, the time cost may vary.
        For a typical HDD, around 20 seconds are required for a 512x512x300 volume.
    This process needs to be executed only once.

    NOTE: if you are using another dataset which contains multiple targets,
        you can modify the variables "ORGAN_NUMBER" and "ORGAN_ID" in run.sh,
        as well as the "is_organ" function in utils.py to define your mapping function flexibly.


![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
**LAZY MODE!**
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)

You can run all the following modules with **one** execution!
  * a) Enable everything (except initialization) in the beginning part.
  * b) Set all the "PLANE" variables as "A" (4 in total) in the following part.
  * c) Run this manuscript!


#### 4.3 Individual training (requires: 4.2)

###### 4.3.1 Check `run.sh` and set `$INDIV_TRAINING_PLANE` and `$INDIV_TRAINING_GPU`.
    You need to run X|Y|Z planes individually, so you can use 3 GPUs in parallel.
    You can also set INDIV_TRAINING_PLANE=A, so that three planes are trained orderly in one GPU.

###### 4.3.2 Set `$ENABLE_INDIV_TRAINING=1` and run this script.
    The following folders/files will be created:
        Under $DATA_PATH/logs/, a log file named by training information.
        Under $DATA_PATH/models/snapshots/, a folder named by training information.
            Snapshots and solver-states will be stored in this folder.
            The log file will also be copied into this folder after the entire training process.
    On the axial view (training image size is 512x512, small input images make training faster),
        each 20 iterations cost ~10s on a Titan-X Pascal GPU, or ~8s on a Titan-Xp GPU.
        As described in the code, we need ~40K iterations, which take less than 5 GPU-hours.
    After the training process, the log file will be copied to the snapshot directory.

###### 4.3.3 Important notes on initialization and model convergence.

![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)
![](https://github.com/198808xc/OrganSegRSTN/blob/master/icon.png)

It is very important to provide a reasonable initialization for our model.
In the previous step of data preparation, we provide a scratch model for the NIH dataset,
in which both the coarse and fine stages are initialized using the weights of an FCN-8s model
(please refer to the [FCN project](https://github.com/shelhamer/fcn.berkeleyvision.org)).
This model was pre-trained on PASCALVOC.
We initialized all upsampling weights to be 0, as the number of channels does not align with that in PASCAL.

The most important thing is to initialize three layers related to saliency transformation,
which are named "score", "score-R" and "saliency" in our prototxts.
In our solution, we use a Xavier filler to fill in the weights of the "score" and "score-R" layers,
and an all-0 cube to fill in the weights of the "saliency" layer.
For the bias term, we use an all-0 vector for "score" and "score-R", and an all-1 vector for "saliency".
*We also set a restart mechanism after the first 10K iterations in case of non-convergece.*
In more than **95% of time**, this mechanism leads to a successful convergence.

###### How to determine if a model converges and works well?

The loss function value in the beginning of training is almost 1.0.
If a model converges, you should observe the loss function values to decrease gradually.
**But in order to make it work well, in the last several epochs,
you need to confirm the average loss function value to be sufficiently low (e.g. 0.15).**
Here we attach the training logs for your reference, see the `logs/` folder (detailed in Section 5).

###### Training RSTN on other CT datasets?

If you are experimenting on other **CT datasets**, we strongly recommend you to use a pre-trained model,
such as those pre-trained model attached in the last part of this file.
We also provide [a mixed model](http://nothing) (to be provided soon),
which was tuned using all X|Y|Z images of 82 training samples for pancreas segmentation on NIH.
Of course, do not use it to evaluate any NIH data, as all cases have been used for training.


#### 4.4 Joint training (requires: 4.3)

###### 4.4.1 Check `run.sh` and set `$JOINT_TRAINING_PLANE` and `$JOINT_TRAINING_GPU`.
    You need to run X|Y|Z planes individually, so you can use 3 GPUs in parallel.
    You can also set JOINT_TRAINING_PLANE=A, so that three planes are trained orderly in one GPU.

###### 4.4.2 Set `$ENABLE_JOINT_TRAINING=1` and run this script.
    The following folders/files will be created:
        Under $DATA_PATH/logs/, a log file named by training information.
        Under $DATA_PATH/models/snapshots/, a folder named by training information.
            Snapshots and solver-states will be stored in this folder.
            The log file will also be copied into this folder after the entire training process.
    On the axial view (training image size is 512x512, small input images make training faster),
        each 20 iterations cost ~10s on a Titan-X Pascal GPU, or ~8s on a Titan-Xp GPU.
        As described in the paper, we need ~40K iterations, which take less than 5 GPU-hours.
    After the training process, the log file will be copied to the snapshot directory.


#### 4.5 Coarse-scaled testing (requires: 4.4)

###### 4.5.1 Check `run.sh` and set `$COARSE_TESTING_PLANE` and `$COARSE_TESTING_GPU`.
    You need to run X|Y|Z planes individually, so you can use 3 GPUs in parallel.
    You can also set COARSE_TESTING_PLANE=A, so that three planes are tested orderly in one GPU.

###### 4.5.2 Set `$ENABLE_COARSE_TESTING=1` and run this script.
    The following folder will be created:
        Under $DATA_PATH/results/, a folder named by training information.
    Testing each volume costs ~30 seconds on a Titan-X Pascal GPU, or ~25s on a Titan-Xp GPU.


#### 4.6 Coarse-scaled fusion (optional) (requires: 4.5)

###### 4.6.1 Fusion is perfomed on CPU and all X|Y|Z planes are combined and executed once.

###### 4.6.2 Set `$ENABLE_COARSE_FUSION=1` and run this script.
    The following folder will be created:
        Under $DATA_PATH/results/, a folder named by fusion information.
    The main cost in fusion includes I/O and post-processing (removing non-maximum components).
        In our future release, we will implement post-processing in C for acceleration.


#### 4.7 Oracle testing (optional) (requires: 4.4)

**NOTE**: Without this step, you can also run the coarse-to-fine testing process.
    This stage is still recommended, so that you can check the quality of the fine-scaled models.

###### 4.7.1 Check `run.sh` and set `$ORACLE_TESTING_PLANE` and `$ORACLE_TESTING_GPU`.
    You need to run X|Y|Z planes individually, so you can use 3 GPUs in parallel.
    You can also set ORACLE_TESTING_PLANE=A, so that three planes are tested orderly in one GPU.

###### 4.7.2 Set `$ENABLE_ORACLE_TESTING=1` and run this script.
    The following folder will be created:
        Under $DATA_PATH/results/, a folder named by training information.
    Testing each volume costs ~10 seconds on a Titan-X Pascal GPU, or ~8s on a Titan-Xp GPU.


#### 4.8 Oracle fusion (optional) (requires: 4.7)

**NOTE**: Without this step, you can also run the coarse-to-fine testing process.
    This stage is still recommended, so that you can check the quality of the fine-scaled models.

###### 4.8.1 Fusion is perfomed on CPU and all X|Y|Z planes are combined and executed once.

###### 4.8.2 Set `$ENABLE_ORACLE_FUSION=1` and run this script.
    The following folder will be created:
        Under $DATA_PATH/results/, a folder named by fusion information.
    The main cost in fusion includes I/O and post-processing (removing non-maximum components).
        In our future release, we will implement post-processing in C for acceleration.


#### 4.9 Coarse-to-fine testing (requires: 4.5)

###### 4.9.1 Check run.sh and set `$COARSE2FINE_TESTING_GPU`.
    Fusion is performed on CPU and all X|Y|Z planes are combined.
    Currently X|Y|Z testing processes are executed with one GPU, but it is not time-comsuming.

###### 4.9.2 Set `$ENABLE_COARSE2FINE_TESTING=1` and run this script.
    The following folder will be created:
        Under $DATA_PATH/results/, a folder named by coarse-to-fine information (very long).
    This function calls both fine-scaled testing and fusion codes, so both GPU and CPU are used.
        In our future release, we will implement post-processing in C for acceleration.

**NOTE**: currently we set the maximal rounds of iteration to be 10 in order to observe the convergence.
    Most often, it reaches an inter-DSC of >99% after 3-5 iterations.
    If you hope to save time, you can slight modify the codes in coarse2fine_testing.py.
    Testing each volume costs ~40 seconds on a Titan-X Pascal GPU, or ~32s on a Titan-Xp GPU.
    If you set the threshold to be 99%, this stage will be done within 2 minutes (in average).


Congratulations! You have finished the entire process. Check your results now!


## 5. Pre-trained Models on the NIH Dataset

**NOTE**: all these models were trained following our default settings.

The 82 cases in the NIH dataset are split into 4 folds:
  * **Fold #0**: testing on Cases 01, 02, ..., 20;
  * **Fold #1**: testing on Cases 21, 22, ..., 40;
  * **Fold #2**: testing on Cases 41, 42, ..., 61;
  * **Fold #3**: testing on Cases 62, 63, ..., 82.

We provide the individually-trained models on each plane of each fold, in total 12 files.

Each of these models is around 1.03GB, approximately the size of two (coarse+fine) FCN models.
  * **Fold #0**: [[X]](https://drive.google.com/open?id=1YtEl7liXz6OLHfljNuTTDmRq8OzcLLlz)
                 [[Y]](https://drive.google.com/open?id=1rqUG3-_dCgSDKPwCjLxs9GKfKJgTVGb6)
                 [[Z]](https://drive.google.com/open?id=14GiE6wKM5wV8ibVlW1KNA-25ULrODyjC)
                 (**Accuracy**: coarse-to-fine 84.61%)
  * **Fold #1**: [[X]](https://drive.google.com/open?id=1Czyp1gIuNBp3iSfI4QGJjEA3orJV_1Fu)
                 [[Y]](https://drive.google.com/open?id=1rluXOLFgBbjNIspT0eBVEPZnge-Df83Y)
                 [[Z]](https://drive.google.com/open?id=1E1V8quDQ0kPx4SPboh6kOycd_mDS5_TR)
                 (**Accuracy**: coarse-to-fine 84.07%)
  * **Fold #2**: [[X]](https://drive.google.com/open?id=1P0VYh245nb28HPXu05Z7pS_oYnv4wCZX)
                 [[Y]](https://drive.google.com/open?id=1Bd0apeNbwZcNME0zTOTMIc1baxAavKdr)
                 [[Z]](https://drive.google.com/open?id=1acQmeUNg1T_qmPa6tEtT3IxbyHqv2iER)
                 (**Accuracy**: coarse-to-fine 84.03%)
  * **Fold #3**: [[X]](https://drive.google.com/open?id=1sV4l6l7zJlQ0ME3kGHND7g1HQzOS_Sx7)
                 [[Y]](https://drive.google.com/open?id=1cMBDhmycrKlQ6igJhoLgp3waDDNcNm45)
                 [[Z]](https://drive.google.com/open?id=15uiqLnWkkqSHKoSIQnz7YH-e_MzTNq0r)
                 (**Accuracy**: coarse-to-fine 85.42%)
  * Average accuracy over 82 cases: 84.53%.

*We ran our codes several times, and the average accuracy varies between 84.4% and 84.6%.*

If you encounter any problems in downloading these files, please contact Lingxi Xie (198808xc@gmail.com).

We also attach the log files for your reference here. Please refer to the `logs/` folder.


## 6. Versions

The current version is v1.0.

You can also view `CHANGE_LOG.txt` for the history of versions.


## 7. Contact Information

If you encounter any problems in using these codes, please open an issue in this repository.
You may also contact Qihang Yu (yucornetto@gmail.com) or Lingxi Xie (198808xc@gmail.com).

Thanks for your interest! Have fun!
