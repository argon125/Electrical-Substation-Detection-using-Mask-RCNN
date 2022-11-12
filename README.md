# Electrical-Substation-Detection-using-Mask-RCNN
Part of ICETCI'21 competition hosted by ISRO and IEEE
IEEE ICETCI: Electrical Substation Detection Competion (TOP 10 Projects)
Date: 20th July, 2021
Submission Version 1

By: Arrun SIvasubramanian, Arun Prakash, Prashanth VR {3rd Year Artificial Intelligence Engineering @ Amrita School of Engineering, Coimbatore}
Guidance: Dr. Sowmya V, Dr. Sajith Varrier {Asst. Professors, Dept. of Computational Engineering and Networking @ Amrita School of Engineering, Coimbatore}


The task of substation detection has been completed using the mask-rcnn model.

The framework of the model used was cloned from the following git repository:
(https://github.com/akTwelve/Mask_RCNN.git updated_mask_rcnn)

This repository has : The MIT License (MIT) Copyright (c) 2017 Matterport, Inc. For more details, refer to: 
https://github.com/akTwelve/Mask_RCNN/blob/master/LICENSE

After cloning this, go to the directory where this is clone dand run the following command:   python setup.py install
Other requirements necessary for this project is enlisted under requirements.txt

This framework has some default parameters such as utils.Config, model etc that is present in mrcnn directory of the repository.
The parameters present in the model were modified according to our custom dataset and necessary steps were taken to train the model.

The first step of the project involves creation of a new project directory. In this, the train images (100 images given) will be saved in image_chips. 
Since the framework needs both training and validation images to minimize the loss, the images were split into the respectie bins inside this directory.

The masks for the images are saved in csv files under folder target_feature (ground truth given)

The above mentioned framework is pre-built to work on images of size 1024x1024. So, the first step is to pad images of size 750x750 to 1024x1024 
on all sides. All the new images were stored in a new directory called image_chips_augmented which will be a part of the root directory. Some geometrical
augmentations were also performed on them(we performed geometrical augmentation using the open source CLoDSA library) to increase the size of the training data
to a total of 270 images and the code for this is found in the SampleGeneration.ipynb notebook.

The next step was to read the bounding box values and convert them to fit the new images of size (1024 x 1024) which is done at the end of the above notebook.

The subsequent steps of training and testing is done in the notebook mrcnn_ver1. The synopsis of the working in that is as follows:
 
The parameters NAME and NUM_CLASSES are modified in the SubstationConfig class. Other parameters set were Detection Confidence and the images per GPU depending
on the specs of or system.

The masks and the images for the are loaded using the prepare function.

The model is initialized, loaded with pre-defined COCO weights present in the root directory (mask_rcnn_coco.h5). The model has two sub-paths: 
One dealing with RegionProposalNetwork(abb. as RPN) and the MRCNN mask detection. The net loss in both these paths are reduced at the end.

The training can be done by 2 steps:
	a) Using layers = "heads" which freezes all layers apart from heads
	b) Using layers = "all" which trains all layers

Since our dataset is unique, we use a hybrid of both these trainings, 1 head + 2 all in Phase1 of training and 1head + 4all in Phase2 of training.
So, once all the images and its corresponding masks have been loaded, we start training the head layers followed by all layers.

After the training is complete, we use the InferenceConfig for prediction. Here we define the two classes: One being the predefined 'BG' class and the other 
being the substation. The results are cross-verified with the validation ground truths.

Running the inference for the test images, all the images are read individually and passed through the network. After obtaining the masks, it's 
converted back to 750 x 750 and are merged together resultung in a 3750x3750 mosaic image.

The final image is then converted to numpy array and saved in the root directory out_imgds.npy
