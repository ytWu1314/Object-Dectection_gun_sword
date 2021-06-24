# 期末作业：物体检测

**本文章参考了 [此链接](http://course.gdou.com/file/source/YOLOv4_win10.pdf)**

## 一、 cuda的配置

* 打开NVIDIA 控制面板，点击系统信息，查看NVVUDA64.DLL

![image-20210609220243258](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210609220243258.png)



* 根据我的电脑的配置以及 [此链接](https://github.com/fo40225/tensorflow-windows-wheel) 找到对应的版本为11.0.2

![image-20210610012502664](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610012502664.png)

* 再访问[CUDA](https://developer.nvidia.com/cuda-downloads) 下载11.0.2版本



## 二、 cudnn的配置

* 11.0版本的CUDA配置 [cudnn 8.0](https://developer.nvidia.com/rdp/cudnn-download)



具体配置过程参照 [CSDN链接1](https://blog.csdn.net/weixin_45023983/article/details/99178625?spm=1001.2014.3001.5506) && [CSDN链接2](https://blog.csdn.net/u010618587/article/details/82940528?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)



## 三、 windows下darknet之yolo(gpu版本）安装 step by step

1. 配置好CUDA和CUDNN
2. 下载VS 2019 由于旧版本官网基本不提供下载了，如需下载请上网找资源
3. 下载opencv 
4. 下载 [darknet](https://link.zhihu.com/?target=https%3A//github.com/AlexeyAB/darknet)  源码下载并压缩



补充及坑点: 

1. opencv 需要配置环境变量，具体可以参考 [有道笔记](https://note.youdao.com/ynoteshare1/index.html?id=04fb326760a726f23cbd9ae8ff6b1fc6&type=note#/)  有配置过其他编译器的往系统环境变量添加都是基本操作。
2. 在安装CUDA的时候需要把"VS"选项勾选

![image-20210610015329242](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610015329242.png)

3. 如果没有此路径 `C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140\BuildCustomizations` 下的文件夹以及文件，需要自己创建。![image-20210610015621536](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610015603066.png)

4. VS需要配置opencv的各种库，避免生成darknet报错，具体可以参考 [有道笔记](https://note.youdao.com/ynoteshare1/index.html?id=04fb326760a726f23cbd9ae8ff6b1fc6&type=note#/)。

5. 修改 darknet\makefile 里面的cuda gpu等参数

6. Windows 解决yolov3 不画框 可以参考[此链接](https://blog.csdn.net/xianma1981/article/details/115459107?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242) 修改parser.c，使cudnn_half 的值为0。（这个是我遇到最费时间的一个坑点）

7. 可以参考此 [B站](https://www.bilibili.com/video/BV1ap4y1e7ng/) 视频，实现生成darknet

8. 在/x64 路径下运行如`darknet detect yolov4.cfg yolov4.weights dog.jpg`  会如下图片说明darknet安装成功。![image-20210610231301948](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610231301948.png)

   

## 四、VOTT准备数据集

用VOTT给`gun_sword_data`  数据集做标记，由于手误，导致sword 拼写错误在后面的swards表示sword

**（也表明我有一步步操作，没有直接抄袭古宇林同学的成果）**

* 用VOTT 1.7 版本将`gun_sword_data\data\train` 里面61张图片坐上标记，分别为`guns`  `swards` 需要耐心一张张做，然后导出 YOLO格式 

![image-20210610233233749](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610233233749.png)



* 标记完成之后导出数据, 把`vott` 导出的`output\data`文件夹里的`obj`文件夹、 `obj.names`文件、`obj.data`文件、`test.txt`文件`和train.txt`文件全都放进`build\darknet\×64\data\`文件夹里. 

![image-20210610232101948](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610232101948.png)



## 五、调模型，训练数据集

1. 在[这里](https://github.com/AlexeyAB/darknet/releases/download/darknet_yolo_v4_pre/yolov4-tiny.conv.29)下载`yolov4-tiny29.conv.29` 文件, 下载完成之后把这个文件放到`build\darknet\×64\`目录下

![image-20210610232229279](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610232229279.png)



2. 在`build\darknet\×64\`目录下新建一个文件`yolo-tiny-obj.cfg`,  把[这个文件](https://github.com/AlexeyAB/darknet/blob/master/cfg/yolov4-tiny.cfg)里的所有内容复制到`yolo-tiny-obj.cfg `中. 

3. 对`yolo-tiny-obj.cfg ` 文件做以下修改: 

* 更改`batch`为`batch = 64`
* 更改`subvision`为`subvision = 16`
* 更改`max_batches`为`max_batches = 6000`, 更改`steps`为`steps = 4800, 5400`
* 更改network size为 `width = 416, height = 416`
* 更改`line220` 和`line269的` `classes` 为`classes = 2`
* 更改`line212` 和 `line263` 的`filters = 255` 为 `filters = 21`   (在有出现yolo的上面)



4. 在`build\darknet\×64`目录下运行命令`darknet.exe detector train data/obj.data yolov4-tiny-obj.cfg yolov4-tiny.conv.29 -map`
5. 训练完成之后得到结果图如下: 



![chart](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/chart.png)





## 六、验证数据集和计算map ##

1. 到[这个网站](https://github.com/Cartucho/mAP)下载这个项目的代码**（之前的input数据删除）**
2. 把验证集中的测试图片放到`map\input\images-optional`目录下

![image-20210610233717596](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610233717596.png)



3. 把用`vott`输出的所有测试图片对应的`.txt`文件放到`map\input\ground-truth`目录下

![image-20210610233737853](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610233737853.png)



4. 把`map\scripts\extra`目录下的`class_list.txt`里面的所有内容删除, **第一行输入guns, 第二行输入swards**, 然后保存文件. 

5. 把`yolo-tiny-obj.cfg` 文件里面的`subvision`和`batch` 改为1

6. 把训练生成的`×64\backup\`文件夹下的`yolov4-tiny-obj_6000.weights` 文件复制到`×64`目录下

7. 在`build\darknet\×64` 目录下运行`darknet.exe detector test data/obj.data yolov4-tiny-obj.cfg yolov4-tiny-obj_6000.weights -dont_show -ext_output < data/test.txt > result.txt -thresh 0.25` 

8. 运行完成之后在`build\darknet\×64` 目录下得到一个`result.txt` 文件, 把这个文件复制粘贴到`map\input\detection_results` 目录下

9. 打开`map\scripts\extra`目录下的`convert_dr_yolo.py` 文件, 把里面的代码全部删掉, 换为以下代码, 然后保存文件, 并且运行该文件. 运行完成之后把上述第八个步骤得到的`result.txt`文件删除掉. 

```python
import os
import re

# make sure that the cwd() in the beginning is the location of the python script (so that every path makes sense)
os.chdir(os.path.dirname(os.path.abspath(__file__)))

IN_FILE = 'result.txt'

# change directory to the one with the files to be changed
parent_path = os.path.abspath(os.path.join(os.getcwd(), os.pardir))
parent_path = os.path.abspath(os.path.join(parent_path, os.pardir))
DR_PATH = os.path.join(parent_path, 'input','detection-results')
#print(DR_PATH)
os.chdir(DR_PATH)

SEPARATOR_KEY = 'Enter Image Path:'
IMG_FORMAT = '.jpg'
result_format = '%'

# outfile = None
fo = open(os.path.join(DR_PATH, 'result.txt'), "r")
alllines = fo.readlines()  #依次读取每行  
   # alllines = alllines.strip()    #去掉每行头尾空白  
# 关闭文件
fo.close()
for line in alllines:
    #if SEPARATOR_KEY in line:
    if IMG_FORMAT in line:
    #    if IMG_FORMAT not in line:
    #        break
        # get text between two substrings (SEPARATOR_KEY and IMG_FORMAT)
        #image_path = re.search(SEPARATOR_KEY + '(.*)' + IMG_FORMAT, line)
        # get the image name (the final component of a image_path)
        # e.g., from 'data/horses_1' to 'horses_1'
        #image_name = os.path.basename(image_path.group(1))
        image_path = (line.split(':', 1))[0]
        image_name = (image_path.split('/',2))[2]
        image_name = (image_name.split('.',1))[0]
        
        
        # close the previous file
        #if outfile is not None:
        #    outfile.close()
        # open a new file
        
    # elif outfile is not None:
    elif result_format in line:
        # split line on first occurrence of the character ':' and '%'
        outfile = open(os.path.join(DR_PATH, image_name + '.txt'), 'w')
        class_name, info = line.split(':', 1)
        confidence, bbox = info.split('%', 1)
        # get all the coordinates of the bounding box
        bbox = bbox.replace(')','') # remove the character ')'
        # go through each of the parts of the string and check if it is a digit
        left, top, width, height = [int(s) for s in bbox.split() if s.lstrip('-').isdigit()]
        right = left + width
        bottom = top + height
        outfile.write("{} {} {} {} {} {}\n".format(class_name, float(confidence)/100, left, top, right, bottom))
        outfile.close()
        
```



10. 进入`map\scripts\extra\`目录, 打开其中的`convert_gt_yolo.py`文件, 把其中`line62` 和`line58` 的图片路径改为`images-optional`, 然后运行该文件.
11. 进入`map\`目录运行`main.py` 文件, 得到计算出来的map结果如下:



![image-20210610232756539](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/image-20210610232756539.png)



**sawrds代表的是swords（手误）**

![mAP](https://github.com/ytWu1314/Object-Dectection_gun_sword/blob/master/images/mAP.png)



## 七、 参考链接

https://zhuanlan.zhihu.com/p/45845454

https://note.youdao.com/ynoteshare1/index.html?id=04fb326760a726f23cbd9ae8ff6b1fc6&type=note#/

https://blog.csdn.net/weixin_45023983/article/details/99178625?spm=1001.2014.3001.5506

https://blog.csdn.net/qq_44222849/article/details/108947642

https://www.bilibili.com/video/BV1ap4y1e7ng/

https://blog.csdn.net/xianma1981/article/details/115459107?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242 
