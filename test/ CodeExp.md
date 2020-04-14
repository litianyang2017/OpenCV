[TOC]

--- 

# HEAD和命名空间

* OpenCV中的C++类和函数都是定义在命名空间cv之内的，有两种方法可以访问：
    1. 在代码开头的加上using namespace cv;
    2. 在使用OpenCV的每一个类和函数时，都加入 cv:: 命名空间

* 写简单的OpenCV程序时，以下三句作为标配：

    * #include <opencv2/core/core.hpp>
    * #include <opencv2/highgui/highgui.hpp>
    * using namespace cv;

* #include <opencv2/opencv.hpp>
    * 为OpenCV头文件的包含

* #include <opencv2/highgui/highgui.hpp>
    * OpenCV highgui模块头文件

* #include <opencv2/imgproc/imgproc.hpp>
    * OpenCV 图像处理头文件

* using namespace cv;
    * 命名空间的包含

---

# Mat类

* Mat类是用于保存图像预计其他矩阵数据的数据结构，默认情况下其尺寸为0.
* 也可以指定其初始尺寸，如定义一个Mat类对象，就要写
    * cv::Mat pic(320, 640, cv::Scalar(100))

* 例：
    * Mat srcImage = imread("dota.jpg")
    * 表示从工程目录下把一幅名为dota.jpg类型的图像载入到Mat类型的srcImage变量中

---

# 图像的载入与显示


## imread()函数

* 用于读取文件中的图片到OpenCV中
* <font color=# size=3>Mat imread(const string& filename, intflags=1)</font>
* **Mat imread(const string& filename, intflags=1)**
    * 第一个参数，载入的图片路径名。
    * 第二个参数，为载入标识，指定一个加载图像的颜色类型。自带默认值1,在调用时可以忽略。如果在调用时忽略这个参数，表示载入三通道的彩色图像。
        * 0: CV_LOAD_IMAGE_GRAYSCCALE 将图像转换成灰度再返回
        * 1: CV_LOAD_IMAGE_COLOR 转换图像到彩色再返回
        * 2： CV_LOAD_IMAGE_ANYDEPTH 如果取这个标识，且载入的图像的深度为16位或者32位，就返回对应深度的图像，否则，就转换为8位图像再返回。
        * 如果输入有冲突的标志，将采用较小的数字值。
        * 若以彩色模式载入图像，解码后的图像会以BGR的通道顺序进行存储，即蓝、绿、红的顺序
        * flags>0 返回一个3通道的彩色图像
        * flags=0 返回灰度图像;
        * flags<0 返回包含Alpha通道的加载图像。
    * 例：
        * Mat image0 = imread("1.jpg", 2|4); 载入无损的源图像
        * Mat image1 = imread("1.jpg", 0); 载入灰度图
        * Mat image2 = imread("1.jpg", 199); 载入3通道的彩色图像

---

## imshow()函数

* 用于在指定的窗口显示一幅图像，函数原型为：
* **void imshow(const string& winname, InputArray mat)**
    * 第一个参数：需要显示的窗口标识名称。
    * 第二个参数：需要显示的图像。

---

## imwrite()函数-输出图像到文件 

* 声明如下：
* **bool imwrite(const string& filename, InputArray img, const vector<int>& params=vector<int>)**
* 第一个参数：需要写入的文件名。注意加上后缀，如“1.jpg”
* 第二个参数：InputArray类型的img，一般填一个Mat类型的图像数据。
* 第三个参数表示为特定搁置保存的参数编码。他有默认值，一般不需要填写。

* 保存的图像拓展名和imread中可以读取的图像拓展名一致。


---

# 4.1基础图像容器Mat

## 4.1.2 Mat结构的使用

* Mat是一个类，由两个数据部分组成
    * 矩阵头（包含矩阵尺寸、存储方法、存储地址等信息）
    * 一个指向存储所有像素值的矩阵（根据所选存储方法的不同，矩阵可以是不同的维数）的指针
* 引用计数机制：
    * 让每个Mat对象有自己的信息头，但共享同一个矩阵。
* 创建只引用部分数据的信息头。如想要创建一个感兴趣区域(ROI),只需要创建包含边界信息的信息头：
    * Mat D (A, Rect(10, 10, 100, 100)); //使用矩阵界定
    * Mat E = A(Range:all(), Range(1,3)); //用行和列界定
* 清理：最后一个使用它的对象。通过引用计数机制来实现。
    * 复制一次，加一次矩阵的引用次数
    * 当一个头被释放后，这个计数减一
    * 当计数值为0，矩阵会被清理。
* 复制矩阵本身（不只是信息头和矩阵指针），可使用函数clone()或者copyTo()
    * Mat F = A.clone()
    * Mat G;
    * A.copyTo(G);
    * 现在改变F或者G不会影响Mat信息头所指向的矩阵。

## 4.1.3 像素值的存储方法

* 存储像素值需要制定颜色空间和数据类型。

* 颜色空间
    * RGB: 人眼类似。基色：红、绿、蓝，有时为了表示透明颜色加入第四个元素alpha(A)
    * HSV和HLS把颜色分解为色调、饱和度和亮度/明度。可以通过抛弃最后一个元素，使算法对输入图像的光照条件不敏感
    * YCrCb 在JPEG图像格式中广泛使用
    * CIE L*a*b* 是一种在感知上均匀的颜色空间，它适合用来度量两个颜色之间的距离。

## 4.1.4 显式创建Mat对象的七种方法

1. 使用Mat()构造函数

    代码：

        Mat M(2,2,CV_8UC3,Scalar(0,0,255));
        cout<<"M = " << endl <<""<<M<<endl<<endl;

    图像尺寸（行数，列数），存储元素的数据类型，每个矩阵点的通道数

    **cv_[位数][带符号与否][类型前缀]C[通道数]**

    CV_8UC3表示8位unsigned char型，每个像素由3个元素组成三通道。

    Scalar是个short型的向量，能使用指定的定制化值来初始化矩阵，还可以用于表示颜色。

2. 在C\C++中通过构造函数进行初始化

    代码：adf

        int sz[3] = {2,2,2};
        Mat L(3,sz,CV_8UC,Scalar::all(0))

    






---







# Main

* imshow 
    * 显示图像
* waitKey(6000)
    * 等待6000ms后窗口自动关闭
    * waitKey(0)
    * 让图片窗口一直显示，等待任意按键按下
* imread 
    * 载入图像

---

# 模块API

## 图像腐蚀

* Mat element = getStructuringElement(MORPH_RECT, Size(15, 15));
    * getStructuringElement返回值为指定形状和尺寸的结构元素（内核矩阵）。
* Mat dstImage;
* erode(srcImage, dstImage, element);
    * erode函数进行图像腐蚀操作