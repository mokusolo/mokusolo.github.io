---
title: 在Kivy中自定义MyCheckBox改变显示尺寸
date: 2018-09-05 21:23:23
tags: [Kivy, python, CheckBox, ToggleButton]
---
[Kivy](https://kivy.org/#home)是一个用于快速开发交互界面应用程序的开源python库，如多点触控应用。
跨平台，支持 Linux, Windows, OS X, Android, iOS, Raspberry Pi。
更多请移步前辈们翻译的[Kivy中文编程指南](https://github.com/Kivy-CN/Kivy-CN)

工作原因，已使用Kivy4个多月，期间遇到了很多问题，大多能通过stackoverflow等平台解决，难解决的也已另辟蹊径或折衷个方法。最终还是决定记录下这些过程。

需求： CheckBox显示的尺寸太小，在10.1寸的显示屏上操作时需要小心翼翼，要将其尺寸改大

参考https://www.reddit.com/r/kivy/comments/424m2o/checkbox_size_scale/， 就知道可以通过自定义一个MyCheckBox来改变其在normal状态（inactive）和down状态（avtive）的背景图片以达到目的。

因Kivy源码checkbox.py中为
```python
class CheckBox(ToggleButtonBehavior, Widget):
    pass
```

查看文档中[ToggleButtonBehavior](https://kivy.org/doc/stable/api-kivy.uix.behaviors.togglebutton.html)处，Kivy已给出很直观的例子:

```python
# main.py

from kivy.app import App
from kivy.uix.image import Image
from kivy.uix.behaviors import ToggleButtonBehavior


class MyButton(ToggleButtonBehavior, Image):
    def __init__(self, **kwargs):
        super(MyButton, self).__init__(**kwargs)
        self.source = 'atlas://data/images/defaulttheme/checkbox_off'

    def on_state(self, widget, value):
        if value == 'down':
            self.source = 'atlas://data/images/defaulttheme/checkbox_on'
        else:
            self.source = 'atlas://data/images/defaulttheme/checkbox_off'


class SampleApp(App):
    def build(self):
        return MyButton()


SampleApp().run()
```

对于我个人的需求，只需找两张合适大小的图片对应两个不同的状态就行（未考虑disabled状态），此处我将图片放在项目的main.py同目录下，为对比显示添加了部分代码，如下
```python
# main.py

from kivy.app import App
from kivy.uix.image import Image
from kivy.uix.behaviors import ToggleButtonBehavior
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.checkbox import CheckBox


class MyButton(ToggleButtonBehavior, Image):
    def __init__(self, **kwargs):
        super(MyButton, self).__init__(**kwargs)
        self.source = './mycheckbox_off.png'

    def on_state(self, widget, value):
        if value == 'down':
            self.source = './mycheckbox_on.png'
        else:
            self.source = './mycheckbox_off.png'


class MyLayout(BoxLayout):
    def __init__(self, **kwargs):
        super(MyLayout, self).__init__(**kwargs)
        kivy_checkbox = CheckBox()
        my_checkbox = MyButton()
        self.add_widget(kivy_checkbox)
        self.add_widget(my_checkbox)


class SampleApp(App):
    def build(self):
        return MyLayout()


SampleApp().run()

```
![](/img/checkbox.png)
