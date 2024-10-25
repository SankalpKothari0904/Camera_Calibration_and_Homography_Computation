# AIM-836 3D Vision
## Assignment - 1
## Submitted by - Sankalp Kothari (IMT2021028)

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

