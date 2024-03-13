# Classic fluent simulation of flow around a two-dimensional cylinder

Flow around a cylinder refers to the flow pattern of low-speed steady flow around a two-dimensional cylinder that is only related to the Re number. When Re≤1, the inertial force in the flow field plays a secondary role compared with the viscous force, and the streamlines upstream and downstream of the cylinder are symmetrical，[Drag coefficient](https://baike.baidu.com/item/%E9%98%BB%E5%8A%9B%E7%B3%BB%E6%95%B0/0?fromModule=lemma_inlink) is approximately Re is inversely proportional (the drag coefficient is 10~60). The flow around this Re number range is called the Stokes zone; as Re increases, the streamlines upstream and downstream of the cylinder gradually lose symmetry. Flow around a cylinder is a very classic experiment, which can also be called Karman vortex street. Let me introduce to you the entire detailed process.Several software will be involved here: FLUENT, ICEM CFD, SPEACECLAIM, pycham.

## 1、icem cfd

Here we use the ANSYS ICEM CFD 2021 R1 version to build a two-dimensional model and divide the structural mesh.

### 1.1 Build a 2D model

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-21-59-06-1709215138069.png)

Click the first icon of Geometry with the left mouse button, and the following screen will appear in the lower left corner.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-01-31-1709215286704.png)

Left-click the XYZ icon and select five points of the 2D model at the following coordinates, for example: (0, 0) (10, 20) (10, -20) (-10, 10) (-10, -10). Each time you enter a point, click apply. As shown below

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-08-57-1709215730732.png)

Connect the points to form a line, left-click the second icon of Geometry--create curve

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-10-42-1709215832735.png)

In the lower left corner, select the first icon--from points

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-12-59-1709215975448.png)

Click the left mouse button to connect two points to form a line, click the middle mouse button to confirm, and connect the closed shapes in sequence. Click the third icon in the picture above and enter 1 in Radius, that is, the radius is 1. Click the left mouse button on (0, 0) as the center of the circle, then left click on a point on any side, and click again to create a circle. Press the middle button to confirm, and you will get the two-dimensional model as shown below

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-23-28-55-1709220528628.png)

Right-click to select Parts in the upper right corner of the picture above, Create part, define the part name of the model, enter the part name, left-click to select the curve, and middle-click to confirm. The left border is: inlet, the upper and lower borders are: wall, the right border is: outlet, and the middle circle is: yuan

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-23-53-15-1709221990400.png)

### 1.2 BLOCKING

After completion, we will do Blocking. This part is still relatively difficult and very important. If it is not done well, it will affect the quality of the mesh. Click the first icon create blocking, select 2D planar in the window type that appears in the lower left corner, and click apply. At this time, we can perform blocking cutting, that is, the second icon split block, left-click to select the edge, drag the line to the appropriate position, and middle-click to confirm. The final result is shown below.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-01-15-10-55-1709277049440.png)

Next we need to associate the model. Click associate, the fifth icon under Blocking. The interface in the lower left corner is as shown below.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-01-20-35-56-1709296549853.png)

Click the first icon to perform point association, that is, the blocking lines and points are associated with the curves and points of the model. Left-click twice on the points on the model to complete the point association; the second icon is line association, select Complete Confirm with the middle mouse button of a curve (blocking three-segment line), select the curve (the line of the model) with the middle mouse button again to confirm, and perform this operation on the four boundaries in sequence; then select the four line segments around the circle to confirm, and the circle association. That's it

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-01-23-24-50-1709306683593.png)

In order to dig out the circle in the model, we need to completely divide the middle circle into a whole part when dividing the blocking. Click the second option ogird block in the split block, select the first select block, select the block where the circle is located in the model to be a square, middle-click to confirm, check around block in the lower left corner of the window, and enter the appropriate offset Click app for the parameters. It is very easy to make mistakes in this step, which may lead to the discovery that other parts have been deleted when deleting the circle! ! ! This may be because the circle and the line of the block are not correctly associated during the association.

After the size is suitable, delete block, select the small square where the circle is, and delete it. The result is as shown below

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-02-14-16-04-1709360159365.png)

At this point the most difficult blocking has been completed and we can divide the mesh.

### 1.3 Meshing

When doing flow around a cylinder, we cannot directly use the system to directly generate the mesh. We need to refine the mesh in some specific areas so that subsequent calculations will be more accurate. Here we use the ninth icon of blocking, Pre-Mesh Params. Select the third icon edge params in the lower left corner of the interface, as shown below.Use the left mouse button to select the divided edge. Here we select the middle line segment of INLET. Because the model is symmetrical, we check Copy Parameters, and all those parallel to this line segment will be selected. Enter the number of nodes to be divided into nodes. The larger the value, the denser the grid.A small arrow will appear on the selected line segment, indicating that the first point starts from that end of the line segment. At this time, we need to encrypt the line segment at both ends, so ratio1 and ratio2 in the figure below must be greater than 1, generally less than 2 That’s it,Just enter an appropriate number smaller than the right side in spacing1 and spacing2. This number is the interval from the first node to the next node. Then click apply to complete the division of this line segment.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-02-22-54-57-1709391292901.png)

Then divide the other two line segments of INLET into nodes. The Ratio near the middle segment should be the same as the ratio value of the corresponding middle segment endpoint, and the node intervals should also be the same. In the same way, for the division of WALL, just repeat this operation.

Division and encryption also need to be performed around the circle to ensure accurate calculations in the later stages. Select one of the four diagonal lines around the circle, enter the appropriate ratio, and node distance to complete the meshing operation.

Click the plus sign in the Blocking area of the upper right window and check pre-mesh to see the divided mesh, as shown below.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-02-23-19-13-1709392745432.png)

This concludes our meshing work. Remember to carefully check the corners of the model to see the quality of the mesh. Some small errors on the model will affect whether FLUENT can be imported later.

Check the mesh quality. Click apply in the lower left corner of the window where Pre-mesh params appears. After that, the number of nodes and grids of the mesh will appear in the small window on the lower right. Then click on the window that appears in the lower left corner of Pre-mesh quality histograms. There is an option. --Criterion can select the appropriate project to check, here we default, and then apply，A histogram of grid quality appears in the lower right corner. Generally, it must be greater than 0.3 to be considered qualified. If the inspection angle is greater than 35°, it is considered qualified. Right-click the histogram and select Reset to start the arrangement from the minimum. The quality of the meshes we generally produce are greater than 0.9.

In addition, when an error occurs, click the icon--Edit mesh's check mesh for special inspection and check the items that need to be checked.The question description will appear in the small window on the smallest surface, and then make corresponding modifications.

### 1.4 Save document

Select Mesh's Load From Blocking at File and the mesh will be generated successfully. Then click Output Mesh on the toolbar, select the first icon below, select Solver, and Apply to save,Then click the file window that pops up in Write Input to change the file name to yuanzhuraoliu. Click save. Icem pops up a small window and click yes. Then the file window pops up to open the yuanzhuraoliu file we just saved. The small window pops up as follows. Select 2D. Change the name of the Output Flie to yuanzhuraoliu, keep the other defaults, and click Done.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-15-09-23-1709449757916.png)

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-16-38-28-3a575121df1241655735d033cfb00af.jpg)

Done With Translation will appear in the small operation feedback window, which indicates that the output is successful. After completing the above operations, the entire modeling and meshing work using ICEM CFD is completed. Next, we need to open FLUENT.

## 2、fluent

ANSYS Fluent is a software dedicated to numerical simulation calculations of fluid mechanics, based on the finite volume method.
When we open FLUENT, the following screen appears.My FLUENT application uses a Chinese interface

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-20-14-29-1709468063008.png)

We select 2D, Solution, and Mesh files in advance, such as the yuanzhuraoliu.msh file saved by Icem cfd above, and then click start with selected options to enter the fluent operation interface.

### 2.1 Steady state calculation

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-20-40-03-1709469599061.png)

The default in the task perspective is to calculate steady state. We click on the model in the summary view window to confirm that all options are turned off. Double-click on the viscosity and select laminar flow.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-20-43-10-1709469785590.png)

Click on the material, select fluid, air, and in the pop-up window, enter 1 for the density and 0.01 for the viscosity. Because what I want to calculate is the flow situation when the Reynolds number is 200. The Reynolds number calculation formula is $Re = \frac{\rho v d}{\mu}$, where v, ρ, and μ are the flow rate, density, and viscosity coefficient of the fluid respectively, and d is a characteristic length. We will also need to set the flow velocity to 2m/s later. When we established the model earlier, the diameter of our circle, that is, the characteristic length, was 1m.

Click on the boundary condition. Here we only need to set the entrance velocity. Double-click the entrance and a small window will pop up. Enter 2 in the velocity field.

Double-click the method and select SIMPLE in the scheme. This is a separated solver, while Coupled is a coupled solver, which is not suitable for our case. Remember to check the Warped-face gradient correction, and keep the others as default.

Next we need to make a report definition. Before that, we need to create a surface. Click Create and select a point to pop up the small window shown below. We need to set the point at the upper right side of the flow around the cylinder to facilitate observation of certain aspects of the flow around the cylinder. These characteristics, we enter point (2, 1), create

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-21-21-22-1709472076030.png)

Double-click the report definition pop-up window, click Create to select the surface report, select Node Average, a small window will pop up and select Velocity for the field variable, then select Y velocity, select point-5 below, and uncheck the report file, as shown below. Click OK to complete the report definition

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-21-27-03-1709472417005.png)

Next, click Initialize, select Hybrid Initialization, then run the calculation, enter 800 for the number of iterations, and keep other options as default. Click Start Calculation, and wait for the calculation to complete to view the results as shown below. We can see that the velocity of Y later shows the characteristics of oscillation and non-convergence. In this way, we know that this case can calculate the Strouhal number, which is also a characteristic of the flow around a cylinder.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-23-38-37-1709480311273.png)

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-23-38-57-1709480329593.png)

Click on the cloud image of the result, select Velocity for the coloring degree, then select Y velocity, select Int-fluent for the surface, and save to view the motion cloud image of the fluid as follows. We can clearly see the shape of the vortex swing.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-23-43-56-image.png)

### 2.2 Transient calculation

Transient calculation can calculate the Strouhal number St in this case, and can also generate fluid motion diagrams, etc. Let's get started

Continuing with the first step in steady-state calculations, the task perspective is changed to: transient state. Leave the others as default. Here we only need to modify the method, changing the previous SIMPLE to PISO. This solver has stronger convergence, and select Bounded Second Order Implicit in the time term discrete format.

Click Control, and the pressure term in the sub-relaxation factor is appropriately increased to 0.5. In this case, it can be increased. In other cases, it is strongly recommended not to change it! !

Click Run Calculation and set the number of time steps, time step size, and maximum number of iterations. Since the speed of the inlet fluid is 2m/s and the model is 35m long, it is necessary to ensure that the fluid flows through the previously created points, so here I set the time to be 0.01 and the number of time steps to 2000, which is enough for the fluid to flow through the points. Here is what I did The result of the calculation. It can be seen that the speed of y can already oscillate periodically.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-21-10-17-1709557809794.png)

#### 2.2.1 Recording and saving of animations

When displaying the cloud image before, we have already set the angle to record, which is the front of the model. Next, we click on the solution animation of the calculation settings. In the small window that appears, select the storage type as HSF file and the animation object as contour-1. Click Preview, adjust to the required angle, and then click Use to activate, as shown below

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-21-21-58-1709558513596.png)

You can choose to automatically save if the calculation time is too long or if there are too many steps to be calculated, just set automatic save to prevent accidents. This case is too simple and automatic saving is not needed.

Click on the method, check No iteration time advancement, and replace PISO with Fractional Step in the plan to calculate faster.

Click Run Calculation, change the time step to a smaller size, and run the calculation.

Click on the resulting animation and click Play to display the small window that appears. You can also save it as MP4 or a picture to your computer. Not shown here

#### 2.2.2 Calculate FFT

Click report definition, create a surface report, node average, keep the same settings as the steady state calculation above, cancel the report graph, and only check the report file

Delete the solution animation file of the calculation settings, turn off automatic saving, and change the time to 0.

Click to run the calculation. Here we only need to calculate 600 time steps. At the end of the calculation, an OUT file will be generated, as shown below. There is data from step 2000 to step 2600 of our previous transient calculation.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-02-19-1709560935563.png)

Click on the drawing of the result, select the small window that pops up from FFT to load the file, find the OUT file you just saved, and after loading it in, change the Y axis to magnitude as height or vector, and the X axis as frequency, and then click Show FFT to get the following results The picture, due to the small sample size, is a bit strange. It is a less standard Fourier transform picture. The scale range of the X-axis here can be changed. The figure below is to 3.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-08-59-1709561330080.png)

Then the FFT calculation of the flow around the entire cylinder ends here. We can only get the basic shape of the line graph, but not the specific st value. Here we need another way to calculate the Strouhal number.

### Calculate Strouhal number using python

In fluid mechanics, the Strouhal number is a dimensionless similarity number that represents the periodic influence in unsteady and constant flows. The calculation formula is usually: $St = \frac{\omega l}{v}$, where ω is the frequency, l is the characteristic length, and v is the free stream velocity. The larger the Strouhal number, the more significant the inertia effect of the fluid; the smaller the Strouhal number, the more significant the viscosity effect of the fluid.

From the previous settings, we know that the fluid speed is 2m/s and the characteristic length is 1m. Next, we will use the code to calculate ω

Here we need to extract the data from the previously saved out file, that is, the x-axis and y-axis data. Here I am using Pycham. I have written the code in advance. Here is my code.

```python
#Plot a graph of y velocity

import matplotlib.pyplot as plt

import numpy as np

#Define the coordinates of the point and enter the data extracted from the Out file below.

x = []

y = []

#Create a new graphic

plt.figure()

#Draw a line chart

plt.plot(x, y)

#Add titles and axis labels

plt.title('折线图示例')

plt.xlabel('X轴')

plt.ylabel('Y轴')

#display graphics

plt.show()

#Fourier transform

fourier_transform = np.fft.fft(y)

yyp = np.abs(fourier_transform)

#Calculate frequency axis

frequencies = np.fft.fftfreq(len(x),x[1]-x[0])

#Take the absolute value of a complex number as the amplitude value

plt.plot(frequencies[frequencies>0],yyp[frequencies>0])

#X-axis labels

plt.xlabel('Frequency (Hz)')

#Y-axis labels

plt.ylabel('Amplitude')

#Chart title

plt.title('Fourier Transform ')

#Show chart

plt.show()

max_y = max(yyp[frequencies > 0])

max_index = np.where(yyp[frequencies > 0] == max_y)[0][0]

#Extract the highest point data, that is, the Strouhal number St

highest_point = (frequencies[max_index], max_y)

print("最高点的坐标为：", highest_point)
```

Click to run the calculation and get the following two pictures, and the Strouhal number when the Reynolds number is 200 is 0.16304348, which is very close to the truth.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-32-10-1709562722657.png)

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-32-40-1709562750882.png)

At this point, the case of flow around a cylinder ends. Thanks for watching. Next time, I will talk about the case of using Fluent to create a three-dimensional cylinder flow around a non-structural grid.
