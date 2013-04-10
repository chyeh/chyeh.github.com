---
layout: post
title: "Multimedia Communications Homework #2"
date: 2013-04-07 15:53
comments: true
categories: 
---

## Algorithm

本次作業的演算法參考課程講義**chap-05**之中**第10到18頁**，**Case Study: Block-based approach**的部分。然而講義當中計算**SASD**的算法並沒有說明邊界blocks的情況。因此我的**SASD值沒有計算那些邊界的blocks**。其中**threshhold**的判定方式，我由觀察測資算出之結果後才加已決定算法。由於有**三個不同的矩陣Y, U, V**，因此照此演算法會算出**三個矩陣的ratio**。如果要檢查是否為**Abrupt Scene Change**，則此三個ratio只要有兩個**大於1.5**，即判定為超過threshhold。若是要檢查是否為**Gradual Scene Change**，則只要有一個**ratio大於2.1**，即判定為超過threshhold。如果**已經判定為Abrupt Scene Change，將不繼續檢查是否為Gradual Scene Change**。

- - -

## 解壓縮

請下指令：

	$ tar -zxvf 0156545-葉治宏-mc102-hw2.tar.gz

- - -

## How to Compile

請移到解壓縮後的目錄底下，直接下make指令:
	$ cd 0156545-葉治宏-mc102-hw2/
	$ make

- - -

## Usage

###使用方法如下：

	$ scp
	Usage: yay [-s <widht>x<heigh>] [-f format] [-p] filename.yuv  
		format can be: 0-Y only, 1-YUV420, 2-YUV422, 3-YUV444
		specify '-p' to enable semi-planar mode

###舉例來說：

若要測試測資**test1.yuv**:
							    
	$ scd -s 176x144 -f 1 test1.yuv
若要測試測資**test2.yuv**:
														    
	$ scd -s 352x288 -f 1 test2.yuv

- - -

## Result

### 第一筆測資`test1.yuv`

	$ scd -s 176x144 -f 1 test1.yuv
	...(省略，那些是我自己寫的debug訊息)
	$ cat output.txt
	31 Abrupt
	65 Gradual

### 第二筆測資`test2.yuv`

	$ rm output.txt						#把test1.yuv的結果先刪除
	$ scd -s 352x288 -f 1 test2.yuv
	...(省略，同上)
	$ cat output.txt
	31 Abrupt
	63 Gradual
	
