# 2D-Image-Reconstruction-To-3D-Model-Using-External-Software
This Project consist of the python programming language using Libraries and different Software for Output

3D Reconstruction from 2D Images
Many advanced tasks in computer vision require determining the three-dimensional position of objects. This project uses mathematical formulas and different software tools to generate a 3D image from a pair of 2D images.

Concept
The idea behind stereo vision in computers is inspired by human depth perception, known as stereopsis. Humans view objects from slightly different angles through each eye because of the horizontal spacing between the eyes. This difference, known as parallax, makes nearby objects appear to shift position relative to background objects when viewed from different perspectives. Using this shift and basic trigonometry, the brain calculates depth.

To recreate this concept using computer vision, we need two 2D images and calibration data from the cameras that took them. Two cameras placed a known distance apart act like a pair of eyes, capturing slightly different views of the same scene. This small shift in image position is called binocular disparity, or simply "disparity." Disparity is calculated as the difference in the horizontal position of the same point in both images:

d = xl - xr

Where:

xl is the x-coordinate of the point in the left image

xr is the x-coordinate in the right image

Assuming the point in space has coordinates P(x, y, z), the depth (z-value) can be computed using proportional relationships from projective geometry:

Left image: x / z = xl / f
Right image: (x - b) / z = xr / f

Here:

b is the baseline, or distance between the two cameras

f is the focal length of the camera lens

From these equations, we derive:

z = (b × f) / (xl - xr)

The key part of 3D reconstruction is calculating the disparity for each point between the two images. One reliable method is called block or region matching. This technique involves selecting a small window of pixels around a point in the left image and finding the most similar window in the right image, usually along the same row (assuming both cameras are aligned at the same height). This process generates a disparity map—a matrix where each value corresponds to the disparity of a pixel. From this map, the 3D coordinates (x, y, z) can be calculated for the entire scene.

## Process

The left and right images were obtained from the 2014 Middlebury Stereo Dataset.


### Pre-processing

The original images were 2864 x 1924, with RGB values. The image was downloaded and converted to grayscale with the Python Pillow image processing library. As the resolution was quite high, the image was resized to ⅛ of the original dimensions to drastically reduce runtime. For further analysis, the intensity values were used to create a NumPy array. A _Process_ class was created to handle these procedures (see `process.py`).


### Calculating Disparity

The __Sum of Absolute Differences (SAD)__ approach was applied to calculate the difference between each pixel inside the two windows (left and right). The pixel in the right image whose window has the smallest SAD value was considered to be the matching pixel, and the distance to that pixel was taken as the disparity. 
 
The SAD method was selected over other methods (e.g. correlation coefficient) for higher accuracy as well as enhanced runtime. The `data/images/calib.txt` file included in the images' source folder provided a conservative search range for region matching (_ndisp_), which was scaled down proportionally to the resized image. As the points in the right image generally displayed a shift in the negative x-direction, the range was chosen as __[- (ndisp * scale_factor), 0]__. The possibility of searching in both positive and negative directions was explored, but resulted in extended runtime and more noise. 


### Post-processing

The resulting plot contained areas of noise and discontinuities, which called for a set of post-processing procedures. 

The noisy pixels were assumed to originate from the "flat" areas, where the disparity values were spiking due to most SAD values being very similar. In order to capture and "denoise" these values, the following steps were introduced:

__Denoising by mode__:
1. Check if the pixel's disparity value exceeds a threshold (>25).
2. If the pixel meets the above criteria, create a relatively large window around the pixel.
3. Calculate the mode (most common value) in the window, and assign it to the pixel.

In order to account for the discontinuous regions among pixels, the following method was utilized for every pixel:

__Smoothing by average__: 

Inside a small window, the average disparity value was calculated. This was compared to the disparity of the pixel in the center, and if the difference was greater than 5 units, the average value was assigned to the pixel. This created a "smoothing" effect, allowing for a more gradual change between two regions.

Furthermore, in order to remove any extraneous noise pixels unaffected by the mode and average methods, all pixels with disparity larger than a threshold was set to a fixed value. This allowed for more distinction between the points of interest during testing as well.

### Obtaining Depth

The following formula, an adaptation of the equation shown in __Theory__ to match the constants listed in `data/images/calib.txt`, can used to calculate the depth value for each pixel.

_**z** = (b * f) / (disparity + doffs)_

## Results

The resulting coordinate values, as well as the RGB values from the original resized image, were collected in a .txt file (see `data/output/`). The information was entered into Cloud Compare, an open source point cloud reconstruction software.

## Execution

python stereo.py ( Before Running Download Cloud Comapare Software which is Free and available Software on the internet for 3D viewing point. )
(Install all libs and make the project Consume By You For EDUCATIONAL Purpose.


Please Support and Like :)
Thak You.

  
