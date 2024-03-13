# ** 二维圆柱绕流经典fluent仿真**

圆柱绕流，是指二维圆柱低速定常绕流的流型只与Re数有关。在Re≤1时，[流场](https://baike.baidu.com/item/%E6%B5%81%E5%9C%BA/0?fromModule=lemma_inlink)中的[惯性力](https://baike.baidu.com/item/%E6%83%AF%E6%80%A7%E5%8A%9B/0?fromModule=lemma_inlink)与粘性力相比居次要地位，圆柱上下游的流线前后对称，[阻力系数](https://baike.baidu.com/item/%E9%98%BB%E5%8A%9B%E7%B3%BB%E6%95%B0/0?fromModule=lemma_inlink)近似与Re成反比(阻力系数为10~60)，此Re数范围的绕流称为斯托克斯区；随着Re的增大，圆柱上下游的流线逐渐失去对称性。圆柱绕流是一个非常经典的实验，也可叫做卡门涡街 ，下面我来给大家介绍整个详细过程，这里会涉及到几个软件：FLUENT,ICEM CFD,SPEACECLAIM，pycham。

## 1、icem cfd

这里我们用到的是ANSYS ICEM CFD 2021 R1版本来建立二维模型以及划分结构性网格。

### 1.1建立二维模型

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-21-59-06-1709215138069.png)

鼠标左键点击 Geometry的第一个图标，左下角会出现以下画面

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-01-31-1709215286704.png)

左键XYZ图标，在下面的坐标选择二维模型的五个点，例如：（0，0）（10，20）（10，-20）（-10，10）（-10，-10）.每输入一个点，点击一次apply。如下图![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-08-57-1709215730732.png)

连接点成线，左键点击Geometry的第二个图标create curve

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-10-42-1709215832735.png)

左下角，选择第一个图标from points

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-22-12-59-1709215975448.png)

鼠标左键点击两个点连成线，鼠标中键确认，依次连接封闭图形。点击上图第三个图标，在Radius处输入1，即半径为1.鼠标左键点击（0，0）为圆心，再左键点击随意一侧的一点，再点击一次则出现圆形，中键确认，即可得到如下图二维模型

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-23-28-55-1709220528628.png)

右键选择上图右上角的Parts，Create part，定义模型的part名称，输入part名称，左键选择曲线，中键确认。左边界为inlet，上下边界为wall，右边界为outlet，中间的圆为yuan

![](C:\Users\1\AppData\Roaming\marktext\images\2024-02-29-23-53-15-1709221990400.png)

### 1.2 BLOCKING

完成之后我们来做Blocking，这一部分还是比较难的，也非常重要，做的不好会影响网格质量。点击第一个图标create blocking在左下角出现的窗口type处选择2D planar，点击apply。这个时候我们就可以进行blocking切割，即第二个图标split block，左键选择边，可拖动线到合适位置，中键确认。最后结果如下图所示。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-01-15-10-55-1709277049440.png)

接下来我们需要给模型做关联，点击Blocking下面的第五个图标associate，左下角的界面如下图

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-01-20-35-56-1709296549853.png)

点击第一个图标进行点关联，即进行blocking的线和点与模型的曲线和点进行关联，左键点击两次模型上的点即可完成点关联；第二个图标为线关联，选择完整的一根曲线鼠标中键确认（blocking的三段线），再选择一次该曲线（模型的线）中键确认，把四条边界依次进行此操作；再选择圆周围的四根线段确认，和圆关联。即可完成

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-01-23-24-50-1709306683593.png)

为了把模型中的圆挖除，我们需要划分blocking时把中间的圆完整的画成一部分。点击split block中的第二个选项ogird block，选择第一个select block，在模型中选择圆所在的块是一个正方形，中键确认，在左下角的窗口的勾选around block，在offset输入合适的参数点击applay。这一步骤非常容易发生错误，导致删除圆块时发现多删除了其他部分！！！这就可能是关联时没有把圆和block的线关联正确。

大小合适之后进行delete block，选择圆所在的小正方形删除即可，结果如下图

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-02-14-16-04-1709360159365.png)

此时最难的blocking已经完成，我们就可以划分网格了

### 1.3划分网格

在做圆柱绕流我们不能直接用系统直接生成网格，要在一些特定区域加密网格，以至于后面的计算更加精确。在这里我们用到blocking的第九个图标Pre-Mesh Params。左下角界面选择第三个图标edge params，如下图。鼠标左键选择划分的边，这里我们选择inlet的中间线段。因为模型是对称的所以我们勾选copy parameters，与这一线段平行的都会选到，在nodes处输入需要划分的节点数，数值越大则网格越密集。在选择的线段上面会出现一个小箭头，表示第一个点从线段的那一头开始，此时，我们需要两端加密这一线段所以在下图中的ratio1和ratio2都要大于1，一般小于2即可，spacing1与spacing2中输入适当的小于右侧的数字即可，这一数字就是第一个节点开始到下一个结点的间隔。然后点击apply完成这一线段的划分。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-02-22-54-57-1709391292901.png)

然后对inlet边的另外两条线段进行划分，在接近中段的ratio要与对应中段端点的ratio数值相同，节点间隔也相同。同理对于wall边的划分，重复这个操作即可。

在圆的周围也需要进行划分加密，保证后期计算准确。选择圆周围的四根斜线中的一根，输入合适的ratio，和节点距离即可完成划分网格的操作

在右上窗口的blocking处点击加号，勾选pre-mesh就能看到划分后的网格，如下图。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-02-23-19-13-1709392745432.png)

到此我们划分网格的工作到此结束。记得仔细检查模型的边边角角，看看网格的质量怎么样，在模型上出现的一些小错误都会影响后期能否导入FLUENT。

检查网格质量，在pre-mesh params出现的左下角窗口点击apply后最下面的小窗口会出现网格的节点数量和网格数量，再点击pre-mesh quality histograms左下角出现的窗口有一个criterion可以选择适当的项目检查，这里我们默认，然后apply，右下角出现网格质量的柱状图，一般要大于0.3才算合格，检查角度的话要大于35°才算合格，右键柱状图选择reset就可以从最小开始排布。我们一般制作的网格质量都大于0.9。

另外出错时点击edit mesh的check mesh进行专项检查，勾选需要检查的项目即可。问题描述会出现在最小面的小窗口，然后在进行相对应的修改

### 1.4保存文件

在file处选择mesh的load from blocking就网格生成成功了，然后点击工具栏的output mesh选择下面第一个图标select solver，apply进行保存，然后点击write input弹出的窗口文件窗口更改文件名，就改为yuanzhuraoliu，点击保存，icem弹出一个小窗口点击yes，然后弹出文件窗口打开刚才我们保存的yuanzhuraoliu文件，弹出小窗口如下，选择2D，在output flie处更改一下名称为yuanzhuraoliu，其他保持默认即可，点击done

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-15-09-23-1709449757916.png)

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-16-38-28-3a575121df1241655735d033cfb00af.jpg)

在操作反馈小窗口会出现done with translation，这就说明输出成功了。完成以上操作就完成了整个用ICEM CFD进行的建模划分网格工作了，接下来我们就需要打开FLUENT了。

## 2、fluent

ANSYS Fluent是一款专用于流体力学数值模拟计算的软件，基于有限体积法。

我们打开fluent，出现以下画面

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-20-14-29-1709468063008.png)

我们提前选择2D，solution，和mesh文件，就比如我们上面icem cfd保存的yuanzhuraoliu.msh文件，然后点击start with selected options进入fluent操作界面。

### 2.1稳态计算

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-20-40-03-1709469599061.png)

在任务视角默认就是计算稳态的，我们在概要视图窗口点击模型确认所有的选项都是关闭的，双击粘性，选择层流

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-20-43-10-1709469785590.png)

点击材料，选择流体，空气，弹出的窗口在密度处输入1，粘度处输入0.01。因为我要计算的是雷诺数为200时的流动情况。雷诺数计算公式是$Re = \frac{\rho v d}{\mu}$，其中v、ρ、μ分别为流体的流速、密度与黏性系数，d为一特征长度。一会还需要设置流速为2m/s，前面建立模型时我们圆的直径即特征长度为1m。

点击边界条件，这里我们只需要设置入口速度，双击入口弹出小窗口在速度大小那一栏输入2

双击方法，在方案处选择simple这是分离求解器，而coupled是耦合求解器不符合我们这个案例，记得勾选warped-face梯度校正，其他保持默认。

接下来我们要做一个报告定义，在此之前我们要创建一个表面，点击创建，选择点弹出下图小窗口，我们要把点设置在圆柱绕流的右上方，以便于观察圆柱绕流的某些特性，我们输入点（2，1），创建。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-21-21-22-1709472076030.png)

双击报告定义弹出的窗口，点击创建选择表面报告，选择节点平均，弹出小窗口在场变量处选择velocity，再选择Y velocity，在下面选择point-5，把报告文件取消勾选，如下图。点击ok即可完成报告定义

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-21-27-03-1709472417005.png)

接下来点击初始化，选择混合初始化，然后运行计算，在迭代次数输入800，其他可以保持默认，点击开始运算，等待计算完成即可以查看结果如下图。我们可以看出Y的速度在后面呈现出了震荡不收敛的特性，如此一来我们就可知此案例可以计算斯特劳哈尔数，这也是圆柱绕流的一个特征。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-23-38-37-1709480311273.png)

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-23-38-57-1709480329593.png)

点击结果的云图，选择着色度为velocity，再选择Y velocity，表面选择int-fluent，保存即可查看流体的运动云图如下，我们可以清晰的看到涡流摆动的形状。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-03-23-43-56-image.png)

### 2.2瞬态计算

瞬态计算可以计算该案例的斯特劳哈尔数St，也能够生成流体的运动动图等等。下面就让我们开始吧

继续在稳态计算第一步的任务视角改为：瞬态。其他保持默认，这里我们只需要从方法开始修改，把之前的SIMPLE改为PISO,这个求解器的收敛性更加强，在时间项离散格式选择bounded second order implicit。

点击控制，在亚松弛因子的压力项适当增加为0.5，这个案例可以增加，其他强烈建议不要更改！！

点击运行计算，设置好时间步数，时间步长，和最大迭代数。由于入口流体的速度为2m/s，模型长35m，要保证流体流过之前创建的点，所以这里我设置时间不长为0.01，时间步数为2000，足够流体流过点了，下面就是我计算的结果。可以看出到后面y的速度已经能够有周期的震荡了。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-21-10-17-1709557809794.png)

#### 2.2.1动画的录制和保存

之前显示云图时我们已经设置好要录制的角度了，就是模型的正面。接下来我们点击计算设置的解决方案动画，出现的小窗口选择储存类型为HSF file，动画对象为contour-1，点击预览，调整到需要的角度，然后点击使用激活即可，如下图。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-21-21-58-1709558513596.png)

可以选择自动保存，计算时间过长或者，计算步数过多的时候防止意外发生就设置自动保存，该案例过于简单就不需要自动保存了。

点击方法，勾选无迭代时间推进，在方案把piso更换为fractional step，可以计算得快一点。

点击运行计算，把时间步长改小一点，运行计算。

点击结果的动画里面，点击播放出现的小窗口可以播放，也可以保存成mp4或者图片到电脑里。这里就不展示了

#### 2.2.2 计算fft

点击报告定义，创建表面报告，节点平均，跟上面稳态计算的设置不变，把报告图取消，只勾选报告文件。

把计算设置的解决方案动画文件删掉，把自动保存关掉，时间间隔改为0即可。

点击运行计算，这里我们只需要计算600个时间步数就够了。计算结束会生成一个OUT文件，如下图，从之前我们瞬态计算的2000步开始一直到2600都有数据。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-02-19-1709560935563.png)

点击结果的绘图，选择FFT弹出的小窗口加载文件，找到刚才保存的OUT文件，加载进来后把Y轴改为magnitude为高度或者说是向量，X轴为频率，然后点击显示FFT，得到以下结果图片，由于样本量小，所以图有点奇怪，是不太标准的傅里叶变换图。这里X轴的刻度范围是可以改的，下图就是到3.

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-08-59-1709561330080.png)

那么整个圆柱绕流计算fft就到此结束了，我们只能得到折线图的基本形状，没有得到具体的st值。这里我们需要另外一种方法计算斯特劳哈尔数。

### python计算斯特劳哈尔数

在流体力学中，斯特劳哈尔数是表征在非定,常流动中周期性影响的一个无量纲相似数。其计算公式通常为：$St = \frac{\omega l}{v}$,其中ω为频率，l为特征长度，v为自由流速度。斯特劳哈尔数越大，则流体的惯性作用越显著；斯特劳哈尔数越小，则流体的黏性作用越显著。

由之前设置可知流体速度为2m/s，特征长度为1m，下面我们就要代码计算出ω

这里我们需要把之前保存的out文件的数据提取出来，即x轴，y轴的数据。这里我用的是Pycham，提前把代码写好以下是我的代码。

```python
#绘制简单图表

import matplotlib.pyplot as plt

import numpy as np

#定义点的坐标,在下面输入out文件提取出来的数据

x = []

y = []

#创建一个新的图形

plt.figure()

#绘制折线图

plt.plot(x, y)

#添加标题和坐标轴标签

plt.title('折线图示例')

plt.xlabel('X轴')

plt.ylabel('Y轴')

#显示图形

plt.show()

#傅里叶变换

fourier_transform = np.fft.fft(y)

yyp = np.abs(fourier_transform)

#计算频率轴

frequencies = np.fft.fftfreq(len(x),x[1]-x[0])

#取复数的绝对值作为振幅值

plt.plot(frequencies[frequencies>0],yyp[frequencies>0])

#X轴标签

plt.xlabel('Frequency (Hz)')

#Y轴标签

plt.ylabel('Amplitude')

#图表标题

plt.title('Fourier Transform ')

#显示图表

plt.show()

max_y = max(yyp[frequencies > 0])

max_index = np.where(yyp[frequencies > 0] == max_y)[0][0]

#提取最高点数据，即斯特劳哈尔数St

highest_point = (frequencies[max_index], max_y)

print("最高点的坐标为：", highest_point)
```

点击运行计算得到以下两张图，和雷诺数为200时的斯特劳哈尔数为0.16304348，非常接近事实了。

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-32-10-1709562722657.png)

![](C:\Users\1\AppData\Roaming\marktext\images\2024-03-04-22-32-40-1709562750882.png)

到这里，圆柱绕流这个案例就结束了。谢谢观看。下次再讲讲用fluent制作非结构性网格的三维圆柱绕流案例。
