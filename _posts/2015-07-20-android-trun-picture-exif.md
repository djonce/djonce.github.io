---
title: Android 三星手机拍照,图片旋转
layout: post
date: 2015-07-20 15:23:12
---

三星手机拍照图片旋转的问题，项目中有上传图片的功能，那么涉及到拍照，从相册中选择图片，别的手机都ok没有问题，唯独三星的手机拍照之后，你会很清楚的看到会把照片旋转一下，然后你根据路径找到的图片就是已经被旋转的了，解决办法终于被我找到了。我们可以根据图片的路径读取照片exif(Exchangeable Image File 可交换图像文件)信息中的旋转角度。   

Android 下的EXIF

根据调试，可以清楚的发现三星手机拍照的图片的旋转角度是90度，而别的手机旋转角度是0度
代码：
{% highlight ruby %}
 /
     * 读取照片exif信息中的旋转角度
     * @param path 照片路径
     * @return角度
     */
    public static int getPictureDegree(String path) {
        int degree  = 0;
        try {
            ExifInterface exifInterface = new ExifInterface(path);
            int orientation = exifInterface.getAttributeInt(ExifInterface.TAG_ORIENTATION, ExifInterface.ORIENTATION_NORMAL);
            switch (orientation) {
                case ExifInterface.ORIENTATION_ROTATE_90:
                    degree = 90;
                    break;
                case ExifInterface.ORIENTATION_ROTATE_180:
                    degree = 180;
                    break;
                case ExifInterface.ORIENTATION_ROTATE_270:
                    degree = 270;
                    break;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return degree;
    }
{% endhighlight %}  

那么我们只需要根据旋转角度将图片旋转过来就OK了   

{% highlight ruby %}

public static Bitmap toturn(Bitmap img){
        Matrix matrix = new Matrix();
        matrix.postRotate(+90); /*翻转90度*/
        int width = img.getWidth();
        int height =img.getHeight();
        img = Bitmap.createBitmap(img, 0, 0, width, height, matrix, true);
        return img;
    }
{% endhighlight %}   
