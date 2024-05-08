---
layout: post
title:  "java在windows下使用opencv实现大图找小图"
date:   2024-05-08 14:49:41
categories: 
   - java
tags:
   - java
---

# java在windows下使用opencv实现大图找小图

## 下载opencv

[官方下载](https://opencv.org/releases/ )

## 安装opencv

将exe解压到指定目录。

在{path}/opencv/build/java下有需要依赖的类库文件。

```
将{path}/opencv/build/java/x64/opencv_java490.dll拷贝至c:\windows\system32下
```

## 引入jar包

```
<dependency>
   <groupId>org.openpnp</groupId>
   <artifactId>opencv</artifactId>
   <version>4.9.0-0</version>
</dependency>
```

## java代码
```
import org.opencv.core.*;
import org.opencv.highgui.HighGui;
import org.opencv.imgcodecs.Imgcodecs;
import org.opencv.imgproc.Imgproc;

import javax.swing.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

public class Test {

    public static void main(String[] args) {
        System.loadLibrary(Core.NATIVE_LIBRARY_NAME);
        String largeImagePath = "/path/1.png";
        String smallImagePath = "/path/2.png";

        boolean isContained;

        Mat largeImage = Imgcodecs.imread(largeImagePath);
        Mat smallImage = Imgcodecs.imread(smallImagePath);

        Mat result = new Mat();
        int resultCols = largeImage.cols() - smallImage.cols() + 1;
        int resultRows = largeImage.rows() - smallImage.rows() + 1;
        result.create(resultRows, resultCols, CvType.CV_32FC1);

        Imgproc.matchTemplate(largeImage, smallImage, result, Imgproc.TM_CCOEFF_NORMED);
        double threshold = 0.8;
        Core.MinMaxLocResult mmr = Core.minMaxLoc(result);

        if (mmr.maxVal >= threshold) {
            Point matchLoc = mmr.maxLoc;
            Imgproc.rectangle(largeImage, matchLoc, new Point(matchLoc.x + smallImage.cols(), matchLoc.y + smallImage.rows()), new Scalar(0, 255, 0));
            HighGui.imshow("Matched Image", largeImage);

            HighGui.waitKey();

            isContained =  true;
        } else {
            isContained = false;
        }
        System.out.println("Is the small image contained in the large image? " + isContained);
    }
}
```