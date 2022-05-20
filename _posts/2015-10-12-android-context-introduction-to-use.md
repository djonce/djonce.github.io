---
title: Android Context使用范围
date:  2015-10-12 22:10:30
layout: post
---

This is a simple Context use table

| 功能                | Application    | Service |  Activity |
| -------------      |:-------------:|:-----:| ------:|
| Starty an Activity | NO1      | NO1   |  YES      |
| Show a Dialog      | NO       | NO    |     YES   |
| Layout Inflation   | YES      |   YES |   YES     |
| Start a Service    | YES      |   YES |    YES    |
| Bind to a Service  | YES      |   YES |   YES     |
| Send a Broadcast   | YES      |   YES |    YES    |
| Eegister BroadcastReceiver| YES|   YES |  YES     |
| Load Resource Values| YES     |    YES |   YES    |

注：NO1标示Application和Service可以启动一个Activity,不过需要创建一个新的task任务栈。而对于Dialog而言，只有在Activity中才能创建
