# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

In my project, i have a custom function called "custom_draw_def_lines" which is my pipeline.
If i can summarize what is in this implementation of my pipeline, it is essentially what was taught in the leasson associated with the project. I made use of all the functions that had been defined in the project.

Here are the steps

- With the input image, first and foremost a grayscale image is created.
- I then apply a Gaussian noise kernel with kernel size = 5
- I then do canny edge detection with low threshold of 50 and high threshold of 150.
- An image mask is then applied and here i do not hard code any vertex index. My area of intereset has 4 vertices and the top two vertices needed fine tuning. The x index of the top two vertices were imshape[1]/2 - 10 and imshape[1]/2 + 10. Basically i get the mid point of te x axis and got 10 points to the left and right. For my y index in these two points, i settled for imshape[0]/2 + 60. I believe these gave me the best results.
- I then perform the hough transform with the following params
    rho = 2
    theta = np.pi/180
    threshold = 15
    min_line_length = 20
    max_line_gap = 15
- Finally i output a weighted image.

I used this function on all 6 test image. The various runs can be seen in the project notebook itself. I  have used subplot to depict the various intermediate outputs along with the final output.

I then proceeded to modify the "draw_lines" function.

- My first objective was to separate lines on the left with those on the right. Given the co-ordinate system that we use in signal processing and in this project, lines with positive slope are on the right and those with negative slope are on the left.
- I have separate lists for x and y indices on the left side and right side.
- The slope is calcuated for only those points where the x index is not same for both points in a line. This is to avoid a division by zero. 
- I filter out point with a slope of zero and take only points where the absolute value of the slope is greater than 0.5
- I settled for the absolute value slope of greater than 0.5 since without it i got sub optimal left side lines on the second test video. I had a few instances where the left side lines would cross the right side line and i wanted to eliminate such lines.
- I leveraged the polyfit function of numpy to find the slope and intercept of the lefta dn right side lines.
- The trick that i employed next was that i knew where all my y indices were going to be. On the lower side, both lower points were going to have a value of imshape[0] for the "y value". On the upper side, both points were going to have a "y value" of imshape[0]/2 + 60.
- With the above i got good results for both the test videos.
- I have a logic to detect whether or not to draw a line and i found this when executing the code for the challenge video itself. The backtrace complained about empty lists while doing polyfit. I realized that there might not be any points to do a polyfit. Hence, i have logic to detect whether or not to doa  polyfit based on the number of points being atleast 2. If so, i proceed with the otehr calculations and final line drawing.


Please NOTE:

I have an extra output image called SolidWhiteRightSegmented. This is the output that is required without making changes to the draw_lines function. The video after making changes to the draw_lines function is SolidWhiteRight.

### 2. Identify potential shortcomings with your current pipeline


The potential shortcomings of my current pipeline are

- My calculations for the x and y indices are essentially replicatd twice. Once while defining my vertices in the pipeline and another time in the draw lines function. I should find some way of propagting this information.
- I have logic to not draw a line if polyfit doesn't get atleast 2 points. I am perhaps wasting a point. This is probably one reason why i got poor results with the challenge video.
- I might possibly have to "tweak" my area of interset a little bit. I might have to redefine with shape of the polygon a little bit.

### 3. Suggest possible improvements to your pipeline

Possible improvements are going to be based on what i observed in my challenge video output.
As i had thought, i might have to further refine the vertices of my area of interest. My are of interest seems more like a triangle.
I will also have to work on filtering lines based on absolute value of slope. I see that when the car approaches a patch of concrete, the line drawn tends to mimic a line across the road rather similar to the concrete patch across the road.

