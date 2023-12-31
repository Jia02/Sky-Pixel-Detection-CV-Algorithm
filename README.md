# Sky Pixels Detection CV Algorithm
The main idea in the proposed **image segmentation** algorithm is the creation of binary mask images by finding the contours of the sky regions based on the colour feature of the image (from 4 [SkyFinder](https://cs.valdosta.edu/~rpmihail/skyfinder/images/index.html) datasets) and draw the contours based on several conditions using the functions provided by **OpenCV** and **NumPy**. 

Based on the mask, the original image can be classified as daytime or nighttime. The mask is further utilised to determine the skyline through **Canny Edge detection** and drawing of the contours. 

This proposed algorithm assumed that a subsequent night-time image is similar to the previous daytime image in terms of visibility and clarity of objects in the scene, except the levels of illumination. 

## Detect & Identify Sky Pixels
1. The self-proposed `determineContour` function incorporates several assumptions to process and draw contours on a mask image, representing the sky region. 
2. OpenCV’s `findContours` with a given threshold is applied to determine the initial contour. 
3. A loop is then executed to check if any contours are present in the image. If contours exist, a blank mask is created to hold the contour information.
4. A matched contour is considered a representation of the sky region and is drawn on the mask when:
    - it has an exact similarity with the maximum contour and starts from the left-most part while occupying at least half of the mask's area based on an assumption[^1]. 

    - it is within the upper part of the image, as determined by the position of its bottom pixel less than or equal to the half of the image’s height based on an assumption[^2]. 

5. A contour is considered a representation of the sky region and is drawn on the mask when: 
    - it is situated from the top portion of the image, has a larger area than a predetermined value and is within the upper part of the image based on an assumption[^3]. 

[^1]: Assume that the sky region is always situated from the top portion of the image.

[^2]: Assume that the sky region occupies the largest portion of the image which corresponds to the maximum contour found.

[^3]: Assume that if a contour is situated from the top portion of the image, has a larger area than a predetermined value and is within the upper part of the image, then it considered as sky region 

> Note: it is noted that due to the lack of sufficient illumination and limited color information typically present in night-time images, the proposed function is unable to determine contours in such scenarios. Instead, a mask is generated for night-time images, resulting in all pixel values being assigned zero. 


## Detect Whether an Image is Taken During Daytime or Nighttime
1. The `detectSkyRegion` function begins by extracting the blue channel of the image, applying OpenCV’s **Gaussian blur**, and using **Otsu's method** for thresholding to create a binary mask. 

2. The total number of non-zero pixels in the mask is calculated:
    - if there are no non-zero pixels (indicating a nighttime image), a flag `isNightImage` is set to True and optionally replaces[^4] the mask with the previous daytime binary mask namely `previous_day_mask`. 

    - else there are non-zero pixels  (indicating a daytime image), the `isNightImage` is set to False, and the `previous_day_mask` is updated with the current mask.

[^4]: Assume that a subsequent night-time image is similar to the previous daytime image in terms of visibility and clarity of objects in the scene, except for the levels of lumination. Hence, a nighttime mask is being replaced by a previous daytime mask based on this assumption. 

## Detect Skyline from an Image
1. **Canny edge detection** is deployed on the input mask to detect edges in the image. The edges is being refined using a OpenCV's **Morphological Closing** operation.

2. Another operation finds contours of the detected edges, and finally draws these contours on a blank image represented as a **NumPy** array to create a skyline representation


## Evaluation through binary image segmentation 
### Qualitative evaluation: 
Comparison of images between input image, ground truth (GT) and predicted mask (Pred.) across 4 datasets. 

### Quantitative evaluation: 
1. **Accuracy** measures the overall correctness of the sky detection algorithm.
    - it is the ratio of sum of True Positive(TP) and True Negative(TN) over the total numer of pixels presented in the ground truth.

    - ***TP binary mask*** corresponds to the number of pixels in the mask image that match with the corresponding pixels in the gt image.

    - ***count of non-zero element*** in TP binary mask corresponds to the number of pixels that are correctly identified as +ve (part of sky pixel region).

    - ***TN binary mask*** corresponds to the number of pixels in the inverted mask image that match with the corresponding pixels in the inverted  gt image.

    - ***count of non-zero element*** in TN binary mask corresponds to the number of pixels that are correctly identified as -ve not part of sky pixel region.

2. **Mean Intersection Over Union (mIOU)** measures the similarity between predicted and ground truth regions. 
    - it calculates the mean ratio of the intersection of the predicted and ground truth regions to their union.

## Run the Program 
1. Download the folders ('623','684','9730','10917','groundTruth') from [Google Drive](https://drive.google.com/file/d/1vCiEC7ZxRDMDpWMdTUSErImtYdLeBys5/view?usp=sharing).
2. Other folders other than mentioned may be ignored. 
3. Store the downloaded folders and main.py file within a new folder. 
4. Ensure the downloaded folders are located within the same directory as the main.py file. 
5. Run the program in main.py. 

