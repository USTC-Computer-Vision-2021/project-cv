# 选题名称 ： 增强现实实现虚拟键盘
## 成员及分工
+ 汪一帆 PB18051161
   - 调研 coding
+ 向凡 PB18061368
   - 撰写文档
## 问题描述
使用opencv实现虚拟键盘，在空中绘制键盘，检测手部位置和姿势，，实现一个空中的虚拟键盘
## 原理分析
先绘制键盘，包括键盘的字母顺序，位置，大小，颜色等设置，初始化摄像头等参数后，使用CVzone计算机视觉包去定位和跟踪食指和中指，当食指和中指靠近特定字母的顶部时，可以键入该字母。使用while(TRUE)循环实现字符串的输入。

![手地标模型]()

如上图所示，手部位置可以用20个点的坐标进行表示，而利用20个点的相对位置可以跟踪手部姿态，这里仅需使用8和12两个点的位置即可，判断食指的位置来确定输入哪一个字母，用食指和中指之间的距离小于设定的阈值来判断是否为有效输入（若大于阈值则认为为误触，不读入键入的字母）。

## 代码实现
* 键盘排布设置为：

```python

   keys = [["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"],
        ["A", "S", "D", "F", "G", "H", "J", "K", "L", ";"],
        ["Z", "X", "C", "V", "B", "N", "M", ",", ".", "/"]]
   finalText = ""
```
* 按键的位置大小及对应文本用list型变量`bottonllist`存储，`botton`为自定义类，自定义函数实现绘制
```python
def drawAll(img, buttonList):
    for button in buttonList:
        x, y = button.pos
        w, h = button.size
        cvzone.cornerRect(img, (button.pos[0], button.pos[1], button.size[0], button.size[1]),
                          20, rt=0)
        cv2.rectangle(img, button.pos, (x + w, y + h), (170, 40, 60), cv2.FILLED)
        cv2.putText(img, button.text, (x + 20, y + 65),
                    cv2.FONT_HERSHEY_PLAIN, 4, (255, 255, 255), 4)
    return img
```
* 按照定位到的手部位置信息，判别输入的字符以及输入是否有效
```python
              if x < lmList[8][0] < x + w and y < lmList[8][1] < y + h:
                    cv2.rectangle(img, (x - 5, y - 5), (x + w + 5, y + h + 5), (175, 0, 175), cv2.FILLED)
                    cv2.putText(img, button.text, (x + 20, y + 65),
                                cv2.FONT_HERSHEY_PLAIN, 4, (255, 255, 255), 4)
                    l, _, _ = detector.findDistance(8, 12, img, draw=False)
                    print(l)

                    ## when clicked
                    if l < 40:
                        keyboard.press(button.text)
                        cv2.rectangle(img, button.pos, (x + w, y + h), (0, 0, 255), cv2.FILLED)
                        cv2.putText(img, button.text, (x + 20, y + 65),
                                    cv2.FONT_HERSHEY_PLAIN, 4, (255, 255, 255), 4)
                        finalText += button.text
                        sleep(0.7)
```
详细参见项目py文件。

## 效果展示
![效果展示]()

键盘底色为蓝色，当某个按键被点击时，按键变为紫色。已经键入的字母将被输出在底部的浅蓝色文本框内。

## 运行说明
需要安装的包：

```
pip install numpy
 
pip install opencv-python
 
pip install cvzone
 
pip install pynput
```
如果安装完所有的包后，仍然存在找不到model的情况，则需检查包的版本信息是否正确。
