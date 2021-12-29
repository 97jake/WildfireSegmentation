# WildfireSegmentation
This repository is a collection of my research done on wildfire segmentation. The idea is to take a satellite image and be able to segment the image into 'burned' and 'not burned'.

## Description of Files - 
Note - this description is in the rough order that you would use the files. For a detailed description of work flow, see below.
  1. SatelliteImageryTutorial.ipynb
    <br>-  Written by Jalen Morgan, this file details how to obtain satellite images from Copernicus OpenAccess Hub. As of creating this repository, this is the best place to get satellite images and therefore data for models.
  2. SentinelAPITutorial.ipynb
    <br>-  This tutorial runs through how to use Sentinel's API. In theory, this could automate data collection and make it possible to obtain large amounts of data for deep learning applications. In practice, the API has proved very unreliable. I stopped using it a couple months ago when an error occured. When I contacted the people at Sentinel about the error, they told me it had been happening a lot and they were working on fixing it (as of Oct 2021). It would definitely be worth while revisiting this tutorial in the future to see if the API could be used to obtain mass satellite images.
  3. BasicImageSegmentation.ipynb
    <br>-  This file goes through 'non-intelligent' methods of segmenting images, 'non-intelligent' referring to methods that don't employ some method of machine learning. These methods are not great but provide for a decent baseline against which you should compare machine learning models. The basic idea is, if these methods perform just as well as a deep learning model, then why bother with the model?
  4. DatabaseConstructor.ipynb
    <br>-  This notebook does most of the work involved in building out a database to use in some machine learning model. The basic work flow is collect satellite images to segment in a single folder, optionally resize those images so that you can upload them quicker to LabelBox, upload the images to LabelBox, segment the burned areas on LabelBox, then run the rest of the notebook using the correct API Key and Project ID. I think that using LabelBox is pretty straight forward, so I won't do a tutorial on that. The downloading process from LabelBox can take a while if you are using a lot of images, so be prepared. The notebook saves images and labels as numpy arrays to .npy files, which can also be pretty big if dealing with a lot of images, so again be prepared.
  5. ImageAugmentation.ipynb
    <br>-  This notebook takes .npy files created in DatabaseConstructor and performs basic image augmentation techniques to expand the dataset. I recommend pulling out a test set of data before running data through this notebook. Otherwise you might get augmented versions of your test data in your train data and introduce heavy data leakage. 
  6. PerimeterDetectionCNN.ipynb
    <br>-  This notebook using PyTorch to build a UNet Convolutional Neural Network to learn how to detect fire boundaries. I think that the network itself is great, it's really a matter of feeding it good data. The data that I've trained on in the past has been very leaky and pretty low quality. I only started with about 30 raw images, almost half of which were essentially duplicates, and then augmented up to over a thousand. I believe this model could do a lot better if work was done to obtain a better dataset. 
    
## Workflow
Basic workflow is divided into three parts - data collection, data augmenting, and model training. 
<br>
<br>Files 1 and 2 are dedicating to obtaining data. See helpful links below for references on sites I've used in the past.
<br>
<br>Files 4 and 5 are the files dedicated to image augmentation. File 4 directs you on how to label your images. Flie 5 is a one stop shop, so all the functions you will need for augmentation will be found there.
<br>
<br>File 6 is dedicated to model training. Using the augmented data obtained from the previous two steps, train the model and measure performance.
<br>

Below are the basic steps to follow - 
1. Find fires using links 1 and 2 below. Any other way to obtain images is welcome. Keep in mind that you will eventually have to provide a labeled version of the image. The current solution is uploading the image to LabelBox and hand-labeling the image. For this reason, you have to be able to see the fire yourself if you are going to hand-label it, which is why I exclusively worked with TCIs (True Color Images) and not various channels. Another reason was they were faster to download. 
2. Create a folder containg .jp2 files that will be used in the labeling process. My code isn't fleshed-out enough to be able to find the .jp2 images or distinguish between different types of files, so make sure that the folder only has satellite images. If not, you are going to have to edit the code to account for the divergent filing system.
3. Open file 4 and change the path variable to point to the folder of satellite images. I recommend running the folder through the resizing function so that the files are a more managable size to upload and download from LabelBox. I used 2048x2048 and it seemed to work nice.
4. Upload the (possibly resized) files to LabelBox. Follow the instructions on the site. A basic overview is create an account, create a new project, select segmentation in the Objects section, create a new dataset, upload images, and then draw the burn region for each image. After that, follow the API instructions on the site to obtain an API key and Project ID. Put those values in to their proper place in file 4 and run the rest of the notebook. 
5. Use file 5 to augment the new image/label pairs. Change 'num' variable to vary the amount of augmentations performed. 
6. Use the .npy files formed from the previous step to train a model. Do this by replacing the path variable with the path to the .npy files, chosing a desired number of epochs, and running the rest of the cells. NOTE - I have a model-saving system in place, so if your runtime times out, run all the cells again but this time changing the scope-function attribute 'loading' to true to load in and use the most recent version of the model. This will resume training where you laast left off.
7. After the model is finished training, turn save_images on or off to display and optionally save different metrics for how well your model did. The last section of file 6 predicts on an optional test set to see how your model did on novel data.


## Helpful Links
1. https://www.fireweatheravalanche.org/fire/
   <br>-This link has a map of recent fires and can be helpful in obtaining coordinate locations of fires so as to locate them in Copernicus
2. https://scihub.copernicus.eu/dhus/#/home
   <br>-Copernicus OpenAccess Hub for downloading satellite images. See tutorials in files 1 and 2 for more information on how to use
3. https://byu.app.box.com/folder/153099301010
   <br>-This is a link to a folder in my research group's box account that contains all of the satellite images I gathered. Unfortunately, the general public won't be able to access this folder and the corresponding data. I haven't been able to find a good place to upload the data so that it will be available to the public, so until I do it will stay on box.
  
