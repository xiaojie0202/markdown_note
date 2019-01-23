# matplotlib
    最流行的Python底层绘图库，主要做数据可视化图表,名字取材于MATLAB，模仿MATLAB构建
## matplotlib 基本要点
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/AB14326D4BDD4BA5BFE050CAB080D750/7541)
axis轴,指的是x或者y这种坐标轴

每个红色的点是坐标,把5个点的坐标连接成一条线,组成了一个折线图
## matplotlib 折线图示例
    以折线的上升或下降来表示统计数量的增减变化的统计图
    特点:能够显示数据的变化趋势，反映事物的变化情况。(变化)

### 示例1
    假设一天中每隔两个小时(range(2,26,2))的气温(℃)分别[15,13,14.5,17,20,25,26,26,27,22,18,15]
```python

from matplotlib import pyplot as plt

"""
假设一天中每隔两个小时(range(2,26,2))的气温(℃)分别是[15,13,14.5,17,20,25,26,26,27,22,18,15]
"""
plt.figure(figsize=(20, 8), dpi=80)
# figure 指的是我们画的图 设置图像的大小(宽20*80,高8*80)

x = range(2, 26, 2)  # X轴的数据

y = [15, 13, 14.5, 17, 20, 25, 26, 26, 27, 22, 18, 15]  # Y轴的数据

plt.plot(x, y)  # 绘图

plt.xticks(x)  # 设置X轴的刻度
plt.yticks(y)  # 设置Y轴的刻度

plt.savefig('q.png')  # 保存图像到q.png， 支持SVG矢量图像
plt.show()
```
>- 执行结果
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/BE74C228E1514D33BFB907AE7219644E/7544)
### 示例2
    如果列表[random.randint(20,35) for i in range(120)]表示10点到12点的每一分钟的气温,如何绘制折线图观察每分钟气温的变化情况?
```python
from matplotlib import pyplot as plt
import random
from matplotlib import font_manager

"""
如果列表a表示10点到12点的每一分钟的气温,如何绘制折线图观察每分钟气温的变化情况?
   a= [random.randint(20,35) for i in range(120)]
"""
my_font = font_manager.FontProperties(fname='msyh.ttc') # 实例化一个字体类，指定字体文件的路径

plt.figure(figsize=(20, 8), dpi=80)  # figure 指的是我们画的图 设置图像的大小(宽20*80,高8*80)
x = [i for i in range(0, 120)]  # X轴的数据
y = [random.randint(20, 35) for i in range(120)]  # Y轴的数据
plt.plot(x, y)  # 绘图

_xticks = ['10点{}分'.format(i) for i in range(0, 60)] + ['11点{}分'.format(i) for i in range(0, 60)]
plt.xticks(x[::3], _xticks[::3], rotation=45, fontproperties=my_font)
# 格式化X轴的显示，两组数据长度必须一样， rotation代表旋转， fontproperties代表使用指定的字体

plt.xlabel('时间', fontproperties=my_font)  # 设置X轴的描述信息
plt.ylabel('温度', fontproperties=my_font)  # 设置Y周的描述信息
plt.title('10点到12点每分钟的气温变化', fontproperties=my_font)  # 设置统计图的标题

plt.grid(alpha=0.6)  # 绘制统计图的表格, alpha设置表格透明度
plt.show()
```
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/7CCE453D187D44D49E20DECBEAEF1019/7550)
### 示例3
    2018年1月1日，和2018/1/2 的10点到12点气温如下，如何绘制折线图观察每分钟气温的变化情况?
       2018/1/1= [random.randint(20,35) for i in range(120)]
       2018/1/2= [random.randint(20,35) for i in range(120)]
```python

from matplotlib import pyplot as plt
import random
from matplotlib import font_manager    
my_font = font_manager.FontProperties(fname='msyh.ttc') # 实例化一个字体类，指定字体文件的路径

plt.figure(figsize=(20, 8), dpi=80)  # figure 指的是我们画的图 设置图像的大小(宽20*80,高8*80)
x = [i for i in range(0, 120)]  # X轴的数据
y_1 = [random.randint(20, 35) for i in range(120)]  # Y轴的数据
y_2 = [random.randint(20, 35) for i in range(120)]  # Y轴的数据
plt.plot(x, y_1, label='2018/1/1', color='orange', linewidth=4)  # 绘制1号的折线图, 线条颜色为orange, 线条粗细为4
plt.plot(x, y_2, label='2018/1/2', color='cyan', linestyle='--')  # 绘制2号的在折线图, 线条颜色为cyan， 线条样式为虚线

_xticks = ['10点{}分'.format(i) for i in range(0, 60)] + ['11点{}分'.format(i) for i in range(0, 60)]
plt.xticks(x[::3], _xticks[::3], rotation=45, fontproperties=my_font)
# 格式化X轴的显示，两组数据长度必须一样， rotation代表旋转， fontproperties代表使用指定的字体

plt.xlabel('时间', fontproperties=my_font)  # 设置X轴的描述信息
plt.ylabel('温度', fontproperties=my_font)  # 设置Y周的描述信息
plt.title('10点到12点每分钟的气温变化', fontproperties=my_font)  # 设置统计图的标题

plt.grid(linestyle=':')  # 绘制统计图的表格 样式为点

# 添加图例（每条线表示什么）
plt.legend(prop=my_font, loc=1)

plt.show()

```
>- 执行结果
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/62C11BA572404D819F04448B1313DB59/7552)
## matplotlib 散点图示例
    散点图:用两组数据构成多个坐标点，考察坐标点的分布,判断两变量
    之间是否存在某种关联或总结坐标点的分布模式。
    特点:判断变量之间是否存在数量关联趋势,展示离群点(分布规律)
### 示例1
```python
from matplotlib import pyplot as plt
import random
from matplotlib import font_manager

y_3 = [11, 17, 16, 11, 12, 11, 12, 6, 6, 7, 8, 9, 12, 15, 14, 17, 18, 21, 16, 17, 20, 14, 15, 15, 15, 19, 21, 22,
           22, 22, 23]
y_10 = [26, 26, 28, 19, 21, 17, 16, 19, 18, 20, 20, 19, 22, 23, 17, 20, 21, 20, 22, 15, 11, 15, 5, 13, 17, 10, 11,
        13, 12, 13, 6]
x_3 = list(range(1, len(y_3)+1))
x_10 = list(range(51, len(y_10)+50+1))

my_font = font_manager.FontProperties(fname='msyh.ttc')  # 实例化一个字体类，指定字体文件的路径
plt.figure(figsize=(20, 8), dpi=80)

# 绘图
plt.scatter(x_3, y_3, label='2016/3')
plt.scatter(x_10, y_10, label='2016/10')

# 设置X轴文字
x = x_3 + x_10
x_ticks = ['三月{}日'.format(i) for i in x_3] + ['10月{}日'.format(i) for i in x_10]
plt.xticks(x[::3], x_ticks[::3], rotation=45, fontproperties=my_font)

# 设置离散图的基本信息
plt.xlabel('时间', fontproperties=my_font)  # 设置X轴的描述信息
plt.ylabel('温度', fontproperties=my_font)  # 设置Y周的描述信息
plt.title('北京2016年3,10月份每天白天的最高气温分布图', fontproperties=my_font)  # 设置统计图的标题

# 绘画表格
plt.grid(linestyle=':')  # 绘制统计图的表格 样式为点

# 添加图例（每条线表示什么）
plt.legend(prop=my_font, loc=1)

plt.show()
```
>- 执行结果
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/AC672C61F34C4B1AAD63FB697A8C1CBB/7554)
## matplotlib 柱形图示例
    条形图:排列在工作表的列或行中的数据可以绘制到条形图中。
    特点:绘制连离散的数据,能够一眼看出各个数据的大小,比较数据之间的差别。(统计)
### 示例1
    假设你获取到了2017年内地电影票房前20的电影(列表a)和电影票房数据(列表b),那么如何更加直观的展示该数据?
    a = ["战狼2","速度与激情8","功夫瑜伽","西游伏妖篇","变形金刚5：最后的骑士","摔跤吧！爸爸","加勒比海盗5：死无对证","金刚：骷髅岛","极限特工：终极回归","生化危机6：终章","乘风破浪","神偷奶爸3","智取威虎山","大闹天竺","金刚狼3：殊死一战","蜘蛛侠：英雄归来","悟空传","银河护卫队2","情圣","新木乃伊",]
    b=[56.01,26.94,17.53,16.49,15.45,12.96,11.8,11.61,11.28,11.12,10.49,10.3,8.75,7.55,7.32,6.99,6.88,6.86,6.58,6.23] 单位:亿
```python
from matplotlib import pyplot as plt
import random
from matplotlib import font_manager
a = ["战狼2", "速度与激情8", "功夫瑜伽", "西游伏妖篇", "变形金刚5：最后的骑士", "摔跤吧！爸爸", "加勒比海盗5：死无对证", "金刚：骷髅岛", "极限特工：终极回归", "生化危机6：终章",
         "乘风破浪", "神偷奶爸3", "智取威虎山", "大闹天竺", "金刚狼3：殊死一战", "蜘蛛侠：英雄归来", "悟空传", "银河护卫队2", "情圣", "新木乃伊", ]
b = [56.01, 26.94, 17.53, 16.49, 15.45, 12.96, 11.8, 11.61, 11.28, 11.12, 10.49, 10.3, 8.75, 7.55, 7.32, 6.99, 6.88,
     6.86, 6.58, 6.23]


my_font = font_manager.FontProperties(fname='msyh.ttc')  # 实例化一个字体类，指定字体文件的路径
plt.figure(figsize=(20, 8), dpi=80)  # figure 指的是我们画的图 设置图像的大小(宽20*80,高8*80)

# 竖向条形图
# plt.bar(a, b)
# plt.xticks(a, fontproperties=my_font, rotation=45)

# 横向条形图
plt.barh(a, b)
plt.yticks(a, fontproperties=my_font)

plt.title('2017年内地电影前20票房电影', fontproperties=my_font)
plt.xlabel('电影', fontproperties=my_font)
plt.ylabel('票房(单位:亿)', fontproperties=my_font)
plt.show()
```
>- 执行结果
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/55D38D7DD97B4041991C548A60B0408B/7556)
### 示例2
    假设你知道了列表a中电影分别在2017-09-14(b_14), 2017-09-15(b_15), 2017-09-16(b_16)三天的票房,为了展示列表中电影本身的票房以及同其他电影的数据对比情况,应该如何更加直观的呈现该数据?
    a = ["猩球崛起3：终极之战","敦刻尔克","蜘蛛侠：英雄归来","战狼2"]
    b_16 = [15746,312,4497,319]
    b_15 = [12357,156,2045,168]
    b_14 = [2358,399,2358,362]
```python
from matplotlib import pyplot as plt
from matplotlib import font_manager
my_font = font_manager.FontProperties(fname='msyh.ttc')  # 实例化一个字体类，指定字体文件的路径
plt.figure(figsize=(20, 8), dpi=80)  # figure 指的是我们画的图 设置图像的大小(宽20*80,高8*80)

# 数据
x = ["猩球崛起3：终极之战", "敦刻尔克", "蜘蛛侠：英雄归来", "战狼2"]
y_14 = [2358, 399, 2358, 362]
y_15 = [12357, 156, 2045, 168]
y_16 = [15746, 312, 4497, 319]

# 初始化X轴的数据
x_14 = list(range(len(x)))
x_15 = [i+0.2 for i in x_14]
x_16 = [i+0.2 for i in x_15]

plt.bar(x_14, y_14, width=0.2, label='2017/9/14')
plt.bar(x_15, y_15, width=0.2, label='2017/9/15')
plt.bar(x_16, y_16, width=0.2, label='2017/9/16')
plt.xticks(x_15, x, fontproperties=my_font)

plt.title('各电影2017/9/14-2017/9/16票房对比', fontproperties=my_font)
plt.xlabel('电影', fontproperties=my_font)
plt.ylabel('票房', fontproperties=my_font)

plt.legend(prop=my_font, loc=1)

plt.show()
```
>- 执行结果
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/51B6F8EFC79D4EFF93D9C46CB3A88626/7560)
## matplotlib直方图示例
    直方图:由一系列高度不等的纵向条纹或线段表示数据分布的情况。 
    一般用横轴表示数据范围，纵轴表示分布情况。
    特点:绘制连续性的数据,展示一组或者多组数据的分布状况(统计)
#### 示例1
    设你获取了250部电影的时长(列表a中),希望统计出这些电影时长的分布状态(比如时长为100分钟到120分钟电影的数量,出现的频率)等信息,你应该如何呈现这些数据?
````python

from matplotlib import pyplot as plt
import random
from matplotlib import font_manager
"""
设你获取了250部电影的时长(列表a中),希望统计出这些电影时长的分布状态(比如时长为100分钟到120分钟电影的数量,出现的频率)等信息,你应该如何呈现这些数据?

:return:
"""
a = [131, 98, 125, 131, 124, 139, 131, 117, 128, 108, 135, 138, 131, 102, 107, 114, 119, 128, 121, 142, 127, 130,
     124, 101, 110, 116, 117, 110, 128, 128, 115, 99, 136, 126, 134, 95, 138, 117, 111, 78, 132, 124, 113, 150, 110,
     117, 86, 95, 144, 105, 126, 130, 126, 130, 126, 116, 123, 106, 112, 138, 123, 86, 101, 99, 136, 123, 117, 119,
     105, 137, 123, 128, 125, 104, 109, 134, 125, 127, 105, 120, 107, 129, 116, 108, 132, 103, 136, 118, 102, 120,
     114, 105, 115, 132, 145, 119, 121, 112, 139, 125, 138, 109, 132, 134, 156, 106, 117, 127, 144, 139, 139, 119,
     140, 83, 110, 102, 123, 107, 143, 115, 136, 118, 139, 123, 112, 118, 125, 109, 119, 133, 112, 114, 122, 109,
     106, 123, 116, 131, 127, 115, 118, 112, 135, 115, 146, 137, 116, 103, 144, 83, 123, 111, 110, 111, 100, 154,
     136, 100, 118, 119, 133, 134, 106, 129, 126, 110, 111, 109, 141, 120, 117, 106, 149, 122, 122, 110, 118, 127,
     121, 114, 125, 126, 114, 140, 103, 130, 141, 117, 106, 114, 121, 114, 133, 137, 92, 121, 112, 146, 97, 137,
     105, 98, 117, 112, 81, 97, 139, 113, 134, 106, 144, 110, 137, 137, 111, 104, 117, 100, 111, 101, 110, 105, 129,
     137, 112, 120, 113, 133, 112, 83, 94, 146, 133, 101, 131, 116, 111, 84, 137, 115, 122, 106, 144, 109, 123, 116,
     111, 111, 133, 150]
# 传入需要统计的数据，以及组数
# 组距: 指每小组的两个端点的距离
# 组数：将数据分组，当数据在100个以内的时候按数据多少常分为5-12组， 计算公式： (max(a)-min(a))极差/组距
plt.hist(a, 20)
plt.show()
````
## https://matplotlib.org/gallery/index.html