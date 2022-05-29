
# 定时器QTimer
- QTimer的主要属性是interval，是定时中断的周期，单位是ms。
- QTimer的主要信号是timeout()，在定时中断时发生此信号，若要在定时中断里做出相应，需要编写与timeout()信号关联的槽函数。
  
> 下面这个代码，运行功能是，可以设置时间跳转周期，1000为1秒，2000为2秒  
> 先输入间隔时间，再点击设置周期  
> 点击开始按钮，则开始显示时间  
> 点击停止按钮，则会在标签中显示，已流逝时间  
> 下面的代码块是源文件  
> ui文件命名为 Test.ui
  
```python

import imp
import sys

from PyQt5 import uic
from PyQt5.QtWidgets import QWidget, QApplication
from PyQt5.QtCore import QTimer, QTime


class Test(QWidget):
    def __init__(self):
        super(Test, self).__init__()  # 调用父类构造函数，创建窗体

        uic.loadUi('ui_files/Test.ui', self)  # 动态导入ui文件

        # 创建定时器
        self.timer = QTimer()
        self.timer.stop()  # 停止
        self.timer.setInterval(1000)  # 定时周期1000ms
        self.timer.timeout.connect(self.do_timer_timeout)

        # 创建计时器
        self.counter = QTime()
    
    ## ============由connectSlotsByName()自动关联的槽函数==================
    def on_btn_start_clicked(self):  # 开始按钮
        self.timer.start()  # 开始定时
        self.counter.start()  # 开始计时
        self.btn_start.setEnabled(False)  # 把开始按钮设置为不可选中
        self.btn_stop.setEnabled(True)
        self.btn_set_interval.setEnabled(False)  # 把设置周期 按钮设置为不可选中
        
    def on_btn_set_interval_clicked(self):  # 设置定时器的周期
        self.timer.setInterval(self.sb_inteval.value())
    
    def on_btn_stop_clicked(self):  # 停止按钮
        self.timer.stop()  # 定时器停止
        time_ms = self.counter.elapsed()  # 计时器经过的毫秒数
        
        ms = time_ms % 1000  # 取余数，毫秒
        sec = time_ms / 1000  # 整秒
        time_str = '经过的时间：%d 秒，%d 毫秒' % (sec, ms)

        self.lb_fly_time.setText(time_str)
        self.btn_start.setEnabled(True)
        self.btn_stop.setEnabled(False)
        self.btn_set_interval.setEnabled(True)
    
    ## ====================================自定义的槽函数==========================
    def do_timer_timeout(self):  # 定义中断响应
        cur_time = QTime.currentTime()  # 获取当前时间
        self.LCD_hour.display(cur_time.hour())
        self.LCD_min.display(cur_time.minute())
        self.LCD_sec.display(cur_time.second())


if __name__ == '__main__':
    app = QApplication(sys.argv)

    pane = Test()
    pane.show()

    sys.exit(app.exec_())

```

> 下面这个例子，显示的当前时间，包括工作日
> ui文件为demo.ui
```python

import sys

from PyQt5.QtWidgets import QWidget, QLabel, QApplication, QPushButton
from PyQt5 import uic
from PyQt5.QtCore import QTimer, QDateTime


class Test(QWidget):
    def __init__(self):
        super(Test, self).__init__()

        uic.loadUi('ui_files/demo.ui', self)
        
        # 定时器
        self.timer = QTimer()
        self.timer.timeout.connect(self.show_time)
    
    def show_time(self):
        # 获取系统现在的时间
        time = QDateTime.currentDateTime()
        # 设置系统时间显示格式
        time_dis = time.toString('yyyy-MM-dd hh:mm:ss dddd')
        # 在标签上显示时间
        self.lb_time.setText(time_dis)
    
    def on_btn_start_clicked(self):
        self.timer.start(1000)
        self.btn_start.setEnabled(False)
        self.btn_stop.setEnabled(True)
    
    def on_btn_stop_clicked(self):
        self.timer.stop()
        self.btn_start.setEnabled(True)
        self.btn_stop.setEnabled(False)
        self.lb_time.clear()


if __name__ == '__main__':
    app = QApplication(sys.argv)

    window = Test()
    window.show()

    sys.exit(app.exec_())


```
