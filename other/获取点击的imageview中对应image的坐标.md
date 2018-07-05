#获取ImageView的触摸点所对应的UIImage的坐标

<!-- TOC depthFrom:2 depthTo:5 withLinks:1 updateOnSave:1 orderedList:0 -->

- [功能描述](#功能描述)
- [实现前分析](#实现前分析)
- [注意事项](#注意事项)
- [代码](#代码)
- [求打赏](#求打赏)

<!-- /TOC -->

##功能描述

在imageview上触摸图片，求对应UIImage的触摸点。

##实现前分析

从imageview上获取触摸点是比较容易的事情，但是由于imageview的大小、比例的关系，获得的触摸点不能直接被认为是UIImage上的点。在这里将会根据大小、比例对触摸点进行操作，以期望得到对应的UIImage的点。
![图片触摸点.png](https://upload-images.jianshu.io/upload_images/13021244-fe7bf7c72f07704d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(蓝色区域为imageview的区域，当我触摸红点时可以轻易的获取该点在imageview上的坐标71.666656,120.000000)

##注意事项
在实践过程中发现如果view出现了旋转的时候，这个时候坐标系会出现旋转，圆心在图片左上角，而view的大小会出现变化，这个时候依照view的大小比例来进行缩放结果将会出现问题（如图所示）。

![pic_imageviewPoint.jpg](https://upload-images.jianshu.io/upload_images/13021244-a60c718837d5b2e1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##代码

```Objective-C
- (CGPoint)getTouchPointOnImageWithImageView:(UIImageView *)imageView
                                  touchPoint:(CGPoint)ivTouchPoint {
    //only support UIViewContentModeScaleAspectFit
    if (!imageView ||
        !imageView.image ||
        imageView.contentMode != UIViewContentModeScaleAspectFit) {

        return CGPointZero;
    }

    //避免被旋转后的原imageview给干扰
    UIImageView *tempIv = [[UIImageView alloc]initWithImage:imageView.image];
    tempIv.transform = imageView.transform;
    tempIv.bounds = imageView.bounds;
    //旋转回去
    arg = atan(tempIv.transform.b / tempIv.transform.a);
    tempIv.transform = CGAffineTransformRotate(tempIv.transform, -arg);

    CGPoint imgTouchPoint = ivTouchPoint;
    BOOL widthHasEmpty = tempIv.image.size.height * tempIv.frame.size.width > tempIv.image.size.width * tempIv.frame.size.height;

    BOOL heightHasEmpty = tempIv.image.size.height * tempIv.frame.size.width < tempIv.image.size.width * tempIv.frame.size.height;

    CGFloat H, h;
    if (heightHasEmpty) {
        H = tempIv.image.size.height * tempIv.frame.size.width / tempIv.image.size.width;
        h = (tempIv.frame.size.height - H) / 2;
    } else {
        H = tempIv.frame.size.height;
        h = 0;
    }

    if (heightHasEmpty) {
        imgTouchPoint.y -= h;
    }

    CGFloat W,w;
    if (widthHasEmpty) {
        W = tempIv.image.size.width * tempIv.frame.size.height / tempIv.image.size.height;
        w = (tempIv.frame.size.width - W) / 2;
    } else {
        W = tempIv.frame.size.width;
        w = 0;
    }
    if (widthHasEmpty) {
        imgTouchPoint.x -= w;
    }

    if (!CGRectContainsPoint(CGRectMake(0, 0, tempIv.image.size.width, tempIv.image.size.height), imgTouchPoint)) {
        NSLog(@"touch point is out of range");
        return CGPointZero;
    }
    return imgTouchPoint;
}
```

##求打赏
![支付合并.jpg](https://upload-images.jianshu.io/upload_images/13021244-bf118185dafa4e39.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
