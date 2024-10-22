# AIM-836 3D Vision
## Assignment - 1
## Submitted by - Sankalp Kothari (IMT2021028)

### Task - 1. Camera Calibration
For the calibration task, I have used OpenCV library built-in method to find a 7x7 grid in the chessboard image.

I first found the chessboard corners using the in-built `findChessboardCornersSB` method to find the corners of the grid and then using all the grids found across the images, we can calibrate the camera.

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
