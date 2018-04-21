---
title: QT延时
tags:
  - QT
categories:
  - wiki
  - QT
date: 2015-04-02 15:39:07
---

# 参考

- [Linux公社：如何让Qt程序Sleep](http://www.linuxidc.com/Linux/2011-07/38543.htm)
- [Norckon's blog:QT延时器函数 The Sleep Function](http://www.fcsys.us/webapp/wordpress/?p=761)
- [zjcxl的博客:QT延时方法](http://blog.sina.com.cn/s/blog_613cfe940100kacm.html)

# 使用QTime

```
    QTime time;
    time.start();
    while(time.elapsed()<1000) // 1000为延时时间
        QCoreApplication::processEvents(); //不停地处理事件，以使得程序保持响应。
```

```
    void sleep(unsigned int msec)
    {
        QTime dieTime = QTime::currentTime().addMSecs(msec);
        while( QTime::currentTime() < dieTime )
            QCoreApplication::processEvents(QEventLoop::AllEvents, 100);
    }
```

# 使用QElapsedTimer

这是Qt4.7引入的新的类，和QTime相比，它提供了更快的计算 elapsed 时间的方法。

```
    QElapsedTimer t;
    t.start();
    while(t.elapsed()<1000)
        QCoreApplication::processEvents();
```

# 使用QEventLoop

```
    QEventLoop eventloop;
    QTimer::singleShot(msec, &eventloop, SLOT(quit()));
    eventloop.exec();
```

# 使用QTimer和QBasicTimer

定时处理，与延时不一样，需要修改程序结构。
