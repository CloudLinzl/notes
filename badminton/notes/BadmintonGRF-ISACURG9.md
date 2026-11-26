---
tags: []
parent: 'BadmintonGRF: A Multimodal Dataset and Benchmark for Markerless Ground Reaction Force Estimation in Badminton'
collections:
    - badminton
$version: 51
$libraryID: 1
$itemKey: ISACURG9

---
# BadmintonGRF
## 希望解决的问题与问题背景，目前方案的局限

## 解决的方法

## 其中的主要数学原理

## 算法架构

## 达成了怎样的效果

## 与现有方案的对比


## 疑点：

1. 直接依赖一台单目相机的帧之间的距离估算z轴速度，忽略了透视关系带来的对z轴距离的影响，但是场地固定在力传感器上削弱了这一影响

2. 对于时间轴对齐与峰值时间，采用人工标注标定相机上的Fz峰值时间，与力传感器上的峰值时间对应完成时间轴对齐
