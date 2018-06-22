#获取ImageView的触摸点所对应的UIImage的坐标

##功能描述

在imageview上触摸图片，求对应UIImage的触摸点。

##实现前分析

从imageview上获取触摸点是比较容易的事情，但是由于imageview的大小、比例的关系，获得的触摸点不能直接被认为是UIImage上的点。在这里将会根据大小、比例对触摸点进行操作，以期望得到对应的UIImage的点。

![pic1](https://github.com/MrYu4/MyUploadPicture/blob/master/图片触摸点.png?raw=true)

(蓝色区域为imageview的区域，当我触摸红点时可以轻易的获取该点在imageview上的坐标(71.666656,120.000000)，然而这并不代表该点在UIImage的坐标也是如此)

```Objective-C
- (CGPoint)getTouchPointOnImageWithImageView:(UIImageView *)imageView
                                  touchPoint:(CGPoint)ivTouchPoint {
    //only support UIViewContentModeScaleAspectFit
    if (!imageView ||
        !imageView.image ||
        imageView.contentMode != UIViewContentModeScaleAspectFit) {

        return CGPointZero;
    }

    CGPoint imgTouchPoint = ivTouchPoint;
    BOOL widthHasEmpty = imageView.image.size.height * imageView.frame.size.width > imageView.image.size.width * imageView.frame.size.height;

    BOOL heightHasEmpty = imageView.image.size.height * imageView.frame.size.width < imageView.image.size.width * imageView.frame.size.height;

    CGFloat H, h;
    if (heightHasEmpty) {
        H = imageView.image.size.height * imageView.frame.size.width / imageView.image.size.width;
        h = (imageView.frame.size.height - H) / 2;
    } else {
        H = imageView.frame.size.height;
        h = 0;
    }

    if (heightHasEmpty) {
        imgTouchPoint.y -= h;
    }

    CGFloat W,w;
    if (widthHasEmpty) {
        W = imageView.image.size.width * imageView.frame.size.height / imageView.image.size.height;
        w = (imageView.frame.size.width - W) / 2;
    } else {
        W = imageView.frame.size.width;
        w = 0;
    }
    if (widthHasEmpty) {
        imgTouchPoint.x -= w;
    }

    if (!CGRectContainsPoint(CGRectMake(0, 0, imageView.image.size.width, imageView.image.size.height), imgTouchPoint)) {
        NSLog(@"touch point is out of range");
        return CGPointZero;
    }
    return imgTouchPoint;
}
```
