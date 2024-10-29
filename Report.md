# AIM-836 3D Vision
## Assignment - 1
## Submitted by - Sankalp Kothari (IMT2021028)
---
---

### Task - 1. Camera Calibration
### 1.1 Data Preparation & Preprocessing
For the calibration task, I have used OpenCV library built-in method to find a 7x7 grid in the chessboard image.

I first found the chessboard corners using the in-built `findChessboardCornersSB` method to find the corners of the grid and then using all the grids found across the images, we can calibrate the camera using the `calibrateCamera` method. (**Note**: this method also returns the )

The chessboard corners are assigned coordinates such as (0,0,0), (1,0,0), and so on (the z-coordinate for any point in the chessboard plane is set to 0). In relation to this world coordinate system, the camera parameters are defined. Only the x and y coordinates of the points change.

The projection matrix can be given as -

$$ P = K\begin{bmatrix} R | t \end{bmatrix} $$

where R is rotation matrix and t is the translation matrix of camera relative to the world frame, and K is the camera matrix.

$$
K=
\begin{bmatrix}
f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1
\end{bmatrix}
$$

The images used are shown below (all the images were taken using my mobile camera) - 
<table>
  <tr>
    <td><img src="./Camera Calibration/Images/1.png" alt="Image 1" width="200"/></td>
    <td><img src="./Camera Calibration/Images/3.png" alt="Image 2" width="200"/></td>
    <td><img src="./Camera Calibration/Images/5.png" alt="Image 3" width="200"/></td>
  </tr>
  <tr>
    <td><img src="./Camera Calibration/Images/2.png" alt="Image 4" width="200"/></td>
    <td><img src="./Camera Calibration/Images/4.png" alt="Image 5" width="200"/></td>
    <td><img src="./Camera Calibration/Images/6.png" alt="Image 6" width="200"/></td>
  </tr>
</table>

These figures have slightly different natural lightings (because it is impossible to replicate the same conditions for each image).

So, I have converted the image to grayscale before applying a thresholding so that we can obtain a high contrast between dark vs light squares in the chessboard and the library function can better identify the chessboard and its corners.

Now, using the `findChessboardCornersSB` method from OpenCV, I found the chessboard corners, which were then refined using `cornerSubPix` method.

The results were as follows - 
<table>
  <tr>
    <td><img src="./Camera Calibration/Outputs/1_new.png" alt="Image 1" width="200"/></td>
    <td><img src="./Camera Calibration/Outputs/3_new.png" alt="Image 2" width="200"/></td>
    <td><img src="./Camera Calibration/Outputs/5_new.png" alt="Image 3" width="200"/></td>
  </tr>
  <tr>
    <td><img src="./Camera Calibration/Outputs/2_new.png" alt="Image 4" width="200"/></td>
    <td><img src="./Camera Calibration/Outputs/4_new.png" alt="Image 5" width="200"/></td>
    <td><img src="./Camera Calibration/Outputs/6_new.png" alt="Image 6" width="200"/></td>
  </tr>
</table>

The chessboard found in the image on the top-right ([5.png](./Camera%20Calibration/Images/5.png)) is not the same as the other 5.

The reason for that can be observed in the grayscale image corresponding to that image.

<table>
  <tr>
    <td><img src="./Camera Calibration/Grayscale/1_gray.png" alt="Image 1" width="200"/></td>
    <td><img src="./Camera Calibration/Grayscale/3_gray.png" alt="Image 2" width="200"/></td>
    <td><img src="./Camera Calibration/Grayscale/5_gray.png" alt="Image 3" width="200"/></td>
  </tr>
</table>

From these images, it is clear that we still have lighting (shadow / glare) issues which weren't resolved by the thresholding. Subsequesntly, we cannot use that image for camera calibration.

We will work with the remaining 5 images to find the intrinsic (camera matrix) and extrinsic (rotation matrix and translation vectors) parameters of the camera.

### 1.2 Results and Evaluation

### Reprojection Error - 
For images 1, 2, 3, 4, and 6, the points are correctly mapped, while in image 5, a different set of points is mapped incorrectly. When the parameters are calculated including image 5, the reprojection error is **0.915**. However, excluding image 5.png results in a much lower reprojection error of **0.456**. 

This reduction in reprojection error indicates improved accuracy when the faulty image is removed from the calculation. This further asserts why we need to exclude image 5.png and the subsequent results will be not considering that particular image.

### Intrinsic Camera Matrix - 
The Intrinsic Camera Matrix (K) is calculated to be -
$$
\text{K} = 
\begin{bmatrix}
418.969 & 0 & 215.825 \\
0 & 418.375 & 291.102 \\
0 & 0 & 1
\end{bmatrix}
$$

### Rotation Matrices & Translation Vectors -

#### Image: [./Images/1.png](./Images/1.png)

**Rotation Matrix:**

$$
\begin{bmatrix}
0.999 & 0.021 & 0.016 \\
-0.021 & 0.999 & 0.029 \\
-0.015 & -0.030 & 0.999 \\
\end{bmatrix}
$$

**Translation Vector (tvec):**

$$
\begin{bmatrix}
-2.928 \\
-2.885 \\
10.068 \\
\end{bmatrix}
$$

#### Image: [./Images/2.png](./Images/2.png)

**Rotation Matrix:**

$$
\begin{bmatrix}
0.999 & 0.021 & 0.016 \\
-0.021 & 0.999 & 0.029 \\
-0.015 & -0.030 & 0.999 \\
\end{bmatrix}
$$

**Translation Vector (tvec):**

$$
\begin{bmatrix}
-2.928 \\
-2.885 \\
10.068 \\
\end{bmatrix}
$$

#### Image: [./Images/3.png](./Images/3.png)

**Rotation Matrix:**

$$
\begin{bmatrix}
0.999 & 0.010 & 0.010 \\
-0.010 & 1.000 & 0.007 \\
-0.010 & -0.007 & 1.000 \\
\end{bmatrix}
$$

**Translation Vector (tvec):**

$$
\begin{bmatrix}
-3.355 \\
-2.651 \\
9.782 \\
\end{bmatrix}
$$

This rotation matrix is partially incorrect as can be seen, this matrix is almost the same as the one corresponding to image 1.png. However, the matrix should've been accomodated to account the 90 degree rotation. But the library function didn't consider that.

#### Image: [./Images/4.png](./Images/4.png)

**Rotation Matrix:**

$$
\begin{bmatrix}
0.815 & 0.579 & 0.008 \\
-0.579 & 0.815 & 0.019 \\
0.005 & -0.020 & 1.000 \\
\end{bmatrix}
$$

**Translation Vector (tvec):**

$$
\begin{bmatrix}
-4.171 \\
-1.186 \\
12.544 \\
\end{bmatrix}
$$

#### Image: [./Images/6.png](./Images/6.png)

**Rotation Matrix:**

$$
\begin{bmatrix}
0.964 & -0.264 & 0.021 \\
0.264 & 0.964 & 0.006 \\
-0.022 & -0.000 & 1.000 \\
\end{bmatrix}
$$

**Translation Vector (tvec):**

$$
\begin{bmatrix}
-1.896 \\
-3.831 \\
10.568 \\
\end{bmatrix}
$$

---
---

### Task - 2. Homography Computation
### 2.1 Homography Computation using Chessboard

I use the chessboard (which is a 2D object) to compute the homographies between the following 2 images.
<table>
  <tr>
    <td><img src="./Homography Computation/Chessboard/Chessboard/1.png" alt="Image 1" width="200"/></td>
    <td><img src="./Homography Computation/Chessboard/Chessboard/2.png" alt="Image 2" width="200"/></td>
  </tr>
</table>

I have used the following 3 methods as my experiments - 

1. Keypoint detection using ORB detector and matching using KNN Matcher
2. Keypoint detection using ORB detector and matching using FLANN Matcher
3. Keypoint detection using Corner detector method and manual matching

All these methods were followed by Homography computation using RANSAC followed by warping the query image and comparing it to the original test image to see the results.

The details for each method are as follows - 

### 2.1.1 Keypoint detection using ORB detector and matching using KNN Matcher
I started by detecting keypoints and creating matches using the ORB detector, followed by Brute Force KNN matching to align the keypoints.

#### **Keypoint Detection with ORB**  
For detecting keypoints, I used the ORB detector, which combines the FAST keypoint detector with the BRIEF descriptor. ORB uses FAST to locate initial keypoints, and then the Harris Corner Measure selects the top N points for better accuracy. ORB also includes rotational invariance for keypoints. This method is faster than SIFT and SURF and often gives better results than SURF. The figure shows the keypoints detected using ORB.

![Keypoints detected using ORB](./Homography%20Computation/Chessboard/Keypoints_BF.png)

#### **Keypoint Matching with KNN**  
For matching keypoints between images, I applied the KNN approach using a Brute-Force (BF) matcher with the L2 norm. I set it to find the two nearest matches for each keypoint and used Lowe's ratio test to keep only the reliable matches, filtering out ambiguous ones. The matches produced are shown below, where some incorrect matches are visible, indicating a higher chance of inaccuracies in the homography matrix. This is explored further in another section. Automated methods can struggle with chessboard images since the repeating patterns often cause similar descriptors for multiple points, whihc leads to possible mismatches.

![Keypoints matched using BF](./Homography%20Computation/Chessboard/Matching_ORB_BF.png)

#### **Homography Computation and Warping results**

**Homography Matrix:**

$$
H = 
\begin{bmatrix}
0.275 & -0.618 & 225.048 \\
0.436 & -0.968 & 352.046 \\
0.001 & -0.003 & 1.000 \\
\end{bmatrix}
$$

**Warping Results:**

![Warping result using ORB and KNN](./Homography%20Computation/Chessboard/Final_BF.png)

### 2.1.2 Keypoint detection using ORB detector and matching using FLANN Matcher

For this approach, I used the same ORB detector as before, so the keypoints identified are identical to the previous method.

**Keypoint Matching with FLANN Matcher**  
The FLANN (Fast Library for Approximate Nearest Neighbors) is a set of algorithms designed for efficient nearest-neighbor searches, performing faster than the Brute Force Matcher. The matching results are shown in the image below. 

As seen, the number of incorrect matches is reduced, which leads to a more reliable homography.

![Keypoints matched using FLANN](./Homography%20Computation/Chessboard/Matching_ORB.png)

#### **Homography Computation and Warping results**

**Homography Matrix:**

$$
H = 
\begin{bmatrix}
0.687 & 0.477 & -68.733 \\
-0.445 & 0.694 & 178.859 \\
0.000 & 0.000 & 1.000 \\
\end{bmatrix}
$$

**Warping Results:**

![Warping result using ORB and FLANN](./Homography%20Computation/Chessboard/Final.png)

### Keypoint detection using Corner detector method and manual matching

For this method, I used the `findChessBoardCornersSB` function (previously used for camera calibration) to detect the chessboard corners, which serve as keypoints. These corners provide natural correspondences between each detected corner.

These matches are directly passed to the function to compute the homography, similar to manually selecting and matching keypoints. This approach results in the highest-quality homography and warping. The detected corners, which act as the keypoints, are shown below.

<table>
  <tr>
    <td><img src="./Homography Computation/Chessboard/1-corners.png" alt="Image 1" width="200"/></td>
    <td><img src="./Homography Computation/Chessboard/2-corners.png" alt="Image 2" width="200"/></td>
  </tr>
</table>

**Homography Matrix:**

$$
H = 
\begin{bmatrix}
0.682 & 0.468 & -66.663 \\
-0.445 & 0.686 & 179.203 \\
0.000 & 0.000 & 1.000 \\
\end{bmatrix}
$$

As we can see, the values in this matrix are fairly close to the values in the Homography Matrix for FLANN matcher and the subsequent warping results will also be very similar as we can see below.

**Warping Results:**

![Warping result using ORB and KNN](./Homography%20Computation/Chessboard/Final_Chessboard_Corners.png)

### 2.2 Homography Computation using Rubik's Cube
