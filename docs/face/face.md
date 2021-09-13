##  人脸识别

## 前言
> [!TIP]
> face_recognition 是一个强大、简单、易上手的人脸识别开源项目，并且配备了完整的开发文档和应用案例，特别是兼容树莓派系统。本项目是世界上最简洁的人脸识别库，你可以使用 Python 和命令行工具提取、识别、操作人脸。本项目的人脸识别是基于业内领先的 C++ 开源库 dlib 中的深度学习模型，用 Labeled Faces in the Wild 人脸数据集进行测试，有高达 99.38% 的准确率。但对小孩和亚洲人脸的识别准确率尚待提升。

## 安装 face_recognition：

首先安装 face_recognition 库：

```bash
pip install face_recognition
```

![](https://img-blog.csdnimg.cn/img_convert/a4c0ed01a16a924c04ef9bd62dcb5ead.png)

实际上只需要安装 face_recognition，当然，没有 opencv 的也需要安装一下 opencv。

```bash
pip install opencv-python
```

## 从图片中找到人脸

![](https://pic.imgdb.cn/item/613ee12444eaada739d8f6a5.jpg)

```python
# 从图片中找到人脸（定位人脸位置）
import face_recognition
import  cv2
#加载图像文件
image = face_recognition.load_image_file("./lyf.png")
image2 = cv2.cvtColor(image,cv2.COLOR_BGR2RGB)
cv2.imshow("src",image2)

face_locations = face_recognition.face_locations(image)
# 循环找到的所有人脸
for face_location in face_locations:
        # 打印每张脸的位置信息
        top, right, bottom, left = face_location
        print("A face is located at pixel location Top: {}, Left: {}, Bottom: {}, Right: {}".format(top, left, bottom, right))
        # 指定人脸的位置信息，然后显示人脸图片
        # face_image = image2[top:bottom, left:right]
        # cv2.imshow("src2", face_image)
        # 矩形框
        cv2.rectangle(image2, (left, top), (right, bottom), (0, 0, 255), 2)
cv2.imshow("dest",image2)
cv2.waitKey(0)
```

## 定位人脸

![](https://img-blog.csdnimg.cn/img_convert/328a00865f5edf1f66b9ce12395035e5.png)

```python
# 找到人脸并将其裁剪打印出来（使用cnn定位人脸）

from PIL import Image
import face_recognition

# Load the jpg file into a numpy array
image = face_recognition.load_image_file("lyf1.jpg")
face_locations = face_recognition.face_locations(image, number_of_times_to_upsample=0, model="cnn")

for face_location in face_locations:
    top, right, bottom, left = face_location
    face_image = image[top:bottom, left:right]
    pil_image = Image.fromarray(face_image)
    pil_image.show()
```


## 识别单张图片中人脸的关键点

调用face_recognition.face_landmarks(image)可识别出人脸关键点信息，包括眼睛、鼻子、嘴巴和下巴等
返回值是包含面部特征字典的列表，列表中每一项对应一张人脸，包括nose_bridge、right_eyebrow、right_eye、chine、left_eyebrow、bottom_lip、nose_tip、top_lip、left_eye几个部分，每个部分包含若干个特征点(x,y)，总共有 68 个特征点。

![](https://pic.imgdb.cn/item/613ee52c44eaada739df7995.jpg)

```python
import cv2
import face_recognition
#加载图像文件
image = face_recognition.load_image_file("lyf1.jpg")
image2 = cv2.cvtColor(image,cv2.COLOR_BGR2RGB)

face_landmarks_list = face_recognition.face_landmarks(image)

facial_features = [
        'chin',
        'left_eyebrow',
        'right_eyebrow',
        'nose_bridge',
        'nose_tip',
        'left_eye',
        'right_eye',
        'top_lip',
        'bottom_lip'
    ]
thickness = 2
point_size = 1
point_color = (0, 255, 0)  # BGR

for face_landmarks in face_landmarks_list:

    for facial_feature in  face_landmarks:
        points_list = face_landmarks[facial_feature]
        print("The {} in this face has the following points: {}".format(facial_feature, points_list))
        # 在图像中画出每个人脸特征！
        for point in points_list:
            cv2.circle(image2, point, point_size, point_color, thickness)

cv2.imshow("dest",image2)
cv2.waitKey(0)
```

**结果**

```bash
I found 1 face(s) in this photograph.
The left_eyebrow in this face has the following points: [(305, 285), (321, 276), (340, 277), (360, 281), (377, 288)]
The right_eye in this face has the following points: [(422, 313), (432, 303), (446, 302), (459, 305), (449, 312), (435, 314)]
The nose_bridge in this face has the following points: [(394, 309), (394, 331), (395, 354), (396, 375)]
The right_eyebrow in this face has the following points: [(407, 287), (424, 278), (442, 273), (461, 272), (478, 279)]
The bottom_lip in this face has the following points: [(429, 409), (419, 421), (408, 428), (398, 430), (389, 429), (377, 424), (364, 412), (370, 413), (389, 414), (398, 415), (407, 413), (423, 411)]
The chin in this face has the following points: [(289, 295), (291, 323), (296, 351), (303, 378), (315, 403), (332, 428), (353, 448), (376, 464), (400, 467), (422, 461), (441, 444), (459, 425), (473, 403), (484, 377), (490, 351), (493, 323), (493, 296)]
The top_lip in this face has the following points: [(364, 412), (377, 407), (389, 403), (397, 406), (406, 402), (417, 405), (429, 409), (423, 411), (406, 412), (397, 414), (389, 413), (370, 413)]
The left_eye in this face has the following points: [(327, 308), (339, 304), (353, 306), (364, 314), (352, 317), (338, 316)]
The nose_tip in this face has the following points: [(375, 383), (386, 387), (396, 390), (407, 385), (416, 381)]
```
face_encodings() 面部编码

```pthon
import face_recognition
#加载图像文件
image = face_recognition.load_image_file("images/ldh2.jpg")

face_encodings = face_recognition.face_encodings(image)
for face_encoding in face_encodings:
    print("face_encoding len = {} \nencoding:{}\n\n".format(len(face_encoding),face_encoding))
```
```bash
face_encoding len = 128
encoding:[-0.12857245 0.2251953 -0.05680346 -0.009356 -0.07961649 -0.01976449
-0.03006363 -0.2188953 0.18227896 -0.06380306 0.23052536 -0.00517259
-0.24484953 -0.1024491 -0.07127093 0.10542907 -0.15712184 -0.24606238
-0.04181587 0.01180026 0.09390671 0.01014723 0.03316356 0.08216273
-0.08543567 -0.27527595 -0.10761252 -0.08561324 0.09562894 -0.06388975
-0.07860459 -0.01040951 -0.21186014 -0.10162984 0.09300554 0.08929634
-0.01133628 -0.02549033 0.25640246 -0.03459882 -0.22288607 0.06690408
0.05515105 0.31292963 0.14994277 0.06461242 0.00211182 -0.18974975
0.11956067 -0.07477805 0.09382927 0.21616243 0.18040094 0.02733615
0.03574533 -0.20213988 -0.00134893 0.08189995 -0.1417242 -0.00179647
0.10290838 -0.06461908 -0.01702856 -0.11400557 0.20824584 0.04568703
-0.07567319 -0.24240926 0.12750657 -0.16503944 -0.10546687 0.14743967
-0.13195604 -0.13922986 -0.26378733 -0.00479815 0.42761648 0.13261758
-0.17136547 0.09223576 -0.04313177 -0.03386433 0.07099032 0.1085121
-0.10494502 0.06504983 -0.15515642 -0.00395807 0.2313281 -0.01442198
-0.08458654 0.14688034 -0.00217457 0.07351732 0.06130501 0.01452726
-0.10001963 0.01601498 -0.12459313 -0.06574792 0.00460516 -0.01154638
-0.00899489 0.13434561 -0.14506203 0.06423949 -0.01264461 0.03912276
-0.06142928 0.01014899 -0.07765882 -0.04298633 0.10122736 -0.29780281
0.25160897 0.09939459 0.02078611 0.08443137 0.10742732 0.06015015
-0.00302691 -0.00466454 -0.20120506 -0.10478879 0.12094288 -0.0389271
0.19183818 -0.00275789]
```

## 人脸匹配
compare_faces()方法可匹配两个面部特征编码，利用两个向量的内积来衡量相似度，根据阈值确认是否是同一人脸。
- 第一个参数给出一个面部编码列表（很多张脸）
- 第二个参数给出单个面部编码（一张脸）
- **默认参数**：tolerance=0.6,可根据自己需求更改，tolerance越小匹配越严格

匹配成功的脸返回True，匹配失败的返回False，顺序与第一个参数中脸的顺序一致

![](https://pic.imgdb.cn/item/613ed47b44eaada739c5c395.jpg)

```python
import face_recognition
known_image = face_recognition.load_image_file("pic1.jpg")
unknown_image = face_recognition.load_image_file("pic2.jpg")

pic_encoding = face_recognition.face_encodings(known_image)[0]
unknown_encoding = face_recognition.face_encodings(unknown_image)[0]

results = face_recognition.compare_faces([pic_encoding], unknown_encoding, tolerance =0.25)    
# A list of True/False valus indicating which known_face_encodings match the face encoding to check

print(type(results))
print(results)

if results[0] == True:
    print("yes")
else:
    print("no")
```


