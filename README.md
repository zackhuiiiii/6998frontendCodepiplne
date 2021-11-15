a.function [center, radius] = findSphere(img)
Parameters: img is a 480x480 matrix
Output: center is the coordinate of the center of the circle (1x2 vector), radius is the radius
Ideas:
Binarize the image with the im2bw function, and then use the regionprops function to find the centroid (center of the circle) and the longest horizontal and vertical distance of the blank area;
The longest horizontal and vertical distance is divided by 4 to be the radius
——————————
b.function light_dirs_5x3 = computeLightDirections(center, radius, img_cell)
Parameters: center is the coordinate of the center of the circle, radius is the radius, img_cell is a cell array storing 5 images (480x480)
Output: light_dirs_5x3 is a matrix (5x3) storing 5 light source direction vectors
Ideas:
Taking the center of the sphere as the origin of the coordinates, the horizontal direction to the right is the positive direction of the x-axis, and the vertical direction upwards is the positive direction of the y-axis.
Then the points on the sphere satisfy x^2+y^2+z^2=radius^2, so the spherical function is z=f(x,y)=(radius^2-x^2-y^2)^0.5 ,
So the normal vector of each point can be calculated as: (-∂z/∂x,-∂z/∂y,1) that is (x/(radius^2-x^2-y^2)^0.5,y /(radius^2-x^2-y^2)^0.5,1).
Assuming that the normal vector direction of the point with the largest pixel value in the image is the direction of the light source (because the normal vector at the point where the light source is directly emitted coincides with the direction vector of the light source, the brightness is the maximum),
Therefore, the light source direction can be obtained by finding the brightest point in the image.
——————————
c.function mask = computeMask(img_cell)
Parameters: img_cell is a cell array storing 5 images (480x480)
Output: The mask is a binary image, the part of 0 is the background, and the part of 1 is the foreground (the part that can reflect light).
——————————
d.function [normals, albedo_img] = computeNormals(light_dirs, img_cell, mask)
Parameters: light_dirs is a matrix (5x3) storing 5 light source direction vectors, img_cell is a cell array storing 5 images (480x480),
The mask is a binary image, the part of 0 is the background, and the part of 1 is the foreground (the part that can reflect light).
Output: normals is an rgb image (480x480x3), and its three color channels r, g, and b respectively store the x, y, and z direction components of the normal vector of each pixel position (after normalization)
albedo_img is a grayscale image (480x480), and the value of each pixel is the modulus of the normal vector of the corresponding position.
Idea: For a certain pixel, if the directions of three light sources and the light intensity (pixel value) of this pixel under the illumination of these three light sources are known,
Through the formula N=(S^-1)*I, the normal vector can be obtained, where N(3x1) is the column vector parallel to the normal vector, and S is the matrix (3x3) storing the directions of the three light sources,
I is a column vector (3x1) that stores 3 light intensities.
After obtaining the N of each pixel, it is normalized and then scaled to 0-255 to obtain the normal vector rgb map.
The normal vector of each pixel is modulated and then normalized to obtain a reflectance map.
