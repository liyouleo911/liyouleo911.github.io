---
layout: post
categories: felink iOS
---

# FLBeautyCameraKit

## 基础相机流程

1. FLBeautyCameraViewController调用FLBeautyCameraManger开始采集

   ```
   [FLBeautyCameraManger startCapture]
   ```

2. FLBeautyCameraManger采集到sampleBuffer回调给FLBeautyCameraViewController

   ```
   [FLBeautyCameraManger captureOutput:(AVCaptureOutput *)captureOutput didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer fromConnection:(AVCaptureConnection *)connection]
   ```

3. FLBeautyCameraViewController收到sampleBuffer回调，转换成pixelBuffer，调用FLBeautyCameraViewHelper显示pixelBuffer

   ```
   [FLBeautyCameraViewController didOutputVideoSampleBuffer:(CMSampleBufferRef)sampleBuffer]
   ```

4. FLBeautyCameraViewHelper调用FLOpenGLImageView显示pixelBuffer 

   ```
   [FLBeautyCameraViewHelper displayPixelBuffer:(CVPixelBufferRef)pixelBuffer][FLOpenGLImageView displayPixelBuffer:(CVPixelBufferRef)pixelBuffer]
   ```

## 自定义播放器流程

1. 创建CADisplayLink并设置回调

   ```
   [CADisplayLink displayLinkWithTarget:self selector:@selector(displayLinkCallback:)]
   ```

2. 创建AVAssetReader并开始读取

   ```
   [FLTestPlayViewController configAssetReader]
   ```

3. 读取videoBuffer并转换成pixelBuffer

   ```
   [FLTestPlayViewController displayLinkCallback:(CADisplayLink *)displatLink]
   [FLTestPlayViewController PixelBufferFromSampleBuffer:(CMSampleBufferRef)nextSampleBuffer]
   ```

4. 调用RenderView显示pixelBuffer

   ```
   [FLOpenGLImageView displayPixelBuffer:(CVPixelBufferRef)pixelBuffer]
   ```

## FaceUnity相机流程[^1]

1. FaceUnityCameraViewController初始化FaceUnity参数

   ```
   [[FUManager shareManager] loadFULiveSDKWithType:(FULiveModelType)type]
   ```

2. FaceUnityCameraViewController调用FLBeautyCameraManger开始采集

   ```objective-c
   [FLBeautyCameraManger startCapture]
   ```

3. FLBeautyCameraManger采集到sampleBuffer回调给FLBeautyCameraViewController

   ```objective-c
   [FLBeautyCameraManger captureOutput:(AVCaptureOutput *)captureOutput didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer fromConnection:(AVCaptureConnection *)connection]
   ```

4. FLBeautyCameraViewController收到sampleBuffer回调，转换成pixelBuffer

   ```
   [FLBeautyCameraViewController didOutputVideoSampleBuffer:(CMSampleBufferRef)sampleBuffer]
   ```

5. FaceUnityCameraViewController美化上一步得到的pixelBuffer，并调用FLBeautyCameraViewHelper显示pixelBuffer

   ```objective-c
   [FaceUnityCameraViewController didVideoBuffer:(CMSampleBufferRef)sampleBuffer pixelBuffer:(CVPixelBufferRef)pixelBuffer]
   ```

6. FLBeautyCameraViewHelper调用FLOpenGLImageView显示pixelBuffer 

   ```objective-c
   [FLBeautyCameraViewHelper displayPixelBuffer:(CVPixelBufferRef)pixelBuffer][FLOpenGLImageView displayPixelBuffer:(CVPixelBufferRef)pixelBuffer]
   ```
   
## 视频预览

1. FLRenderVideoViewController通过FLAudioVideoReader读取视频和播放音频

   ```
   [FLAudioVideoReader startReading]
   ```

2. FLAudioVideoReader通过displayLinkCallback回调，采集视频pixelBuffer并回调给FLRenderVideoViewController

   ```
   [FLAudioVideoReader displayLinkCallback:(CADisplayLink *)displatLink]
   ```

3. FLRenderVideoViewController调用FLRenderVideoViewHelper显示pixelBuffer

   ```
   [FLRenderVideoViewController videoReaderDidReadVideoBuffer:(CVPixelBufferRef)pixelBuffer]
   ```

4. FLRenderVideoViewHelper调用FLOpenGLImageView显示pixelBuffer

   ```
   [FLRenderVideoViewHelper displayPixelBuffer:(CVPixelBufferRef)pixelBuffer]
   [FLOpenGLImageView displayPixelBuffer:(CVPixelBufferRef)pixelBuffer]
   ```

## 要脸相机预览(不使用模板)

1. FaceUnityVideoPreviewController通过FLAudioVideoReader读取视频和播放音频

   ```
   [FLAudioVideoReader displayLinkCallback:]
   [FLAudioVideoReader readVideoBuffer]
   ```

2. FLAudioVideoReader采集视频pixelBuffer并回调给FaceUnityVideoPreviewController

   ```
   [FaceUnityVideoPreviewController videoReaderDidReadVideoBuffer:]
   ```

3. FaceUnityVideoPreviewController美化上一步得到的pixelBuffer，并调用FLRenderVideoViewHelper显示pixelBuffer

   ```
   [FLRenderVideoViewHelper displayPixelBuffer:]
   ```

4. FLRenderVideoViewHelper调用FLOpenGLImageView显示pixelBuffer

   ```
   [FLOpenGLImageView displayPixelBuffer:]
   ```

## 要脸相机预览(使用自定义特效)[^2]

1. FaceUnityVideoPreviewController(CustomEffect)初始化特效

   ```
   [FaceUnityVideoPreviewController(CustomEffect) selectCustomEffectWithPreset:]
   ```

2. FLGPUOutput的子类初始化视频地址和View的位置

   ```
   [FLGaussianBlurOutput initWithSuperView:videoURL:]等等
   ```

3. 初始化FLGPUImageMovieOutput

   ```
   [FLGPUImageMovieOutput initWithURL:isPlaySound:isRepeatPlay:]
   ```

4. 初始化FLGPUImageMovie

   ```
   [FLGPUImageMovie initWithPlayerItem:]或[FLGPUImageMovie initWithURL:]
   ```

5. 接收GPUImageMovie的回调

   ```
   [GPUImageMovie displayLinkCallback:]
   ```

6. FLGPUImageMovie重写GPUImageMovie的processMovieFrame的方法，用于处理pixelBuffer

   ```
   [FLGPUImageMovie processMovieFrame:withSampleTime:] 
   ```

7. 交由FLGPUImageMovie的movieDelegate去处理pixelBuffer

   ```
   [FLGPUOutput processVideoPixelBuffer:]
   ```

8. FLGPUOutput再转给它自己的movieDelegate去处理pixelBuffer

   ```
   [FaceUnityVideoPreviewController(CustomEffect) processVideoPixelBuffer:]
   ```

9. FaceUnityVideoPreviewController(CustomEffect)调用FaceUnity美化，并返回美化后的pixelBuffer给FLGPUImageMovie

   ```
   [[FUManager shareManager] renderItemsToPixelBuffer:]
   ```

10. FLGPUOutput的子类将美化过的pixelBuffer叠加特效

   ```
   [GPUImageMovie processMovieFrame:withSampleTime:]
   ```

   

## 要脸相机预览(使用自定义模板)[^3]

1. FaceUnityVideoPreviewController(CustomTemplate)初始化模板

   ```
   [FaceUnityVideoPreviewController(CustomTemplate) startCustomTemplate]
   ```

2. FLCameraManager开始播放视频，同时开始Filter

   ```
   [FLCameraManager startPlayVideo:volume:]
   [FLCameraManager startFilterAction]
   ```

3. 初始化FLGPUImageMovie

   ```
   [FLVideoOutputSource createOutputSourceWithURL:volume:]
   [FLGPUImageMovie initWithPlayerItem:]
   ```

4. 接收GPUImageMovie的回调

   ```
   [GPUImageMovie displayLinkCallback:]
   ```

5. FLGPUImageMovie重写GPUImageMovie的processMovieFrame的方法，用于处理pixelBuffer

   ```
   [FLGPUImageMovie processMovieFrame:withSampleTime:] 
   ```

6. 交由FLGPUImageMovie的movieDelegate去处理pixelBuffer

   ```
   [FLVideoOutputSource processVideoPixelBuffer:]
   ```

7. FLVideoOutputSource转发给它自己的代理去处理pixelBuffer

   ```
   [FLCameraManager processVideoPixelBuffer:]
   ```

8. FLCameraManager再转给它自己的代理去处理pixelBuffer

   ```
   [FaceUnityVideoPreviewController(Template) processVideoPixelBuffer:]
   ```

9. FaceUnityVideoPreviewController(Template)调用FaceUnity美化，并返回美化后的pixelBuffer给FLGPUImageMovie

   ```
   [[FUManager shareManager] renderItemsToPixelBuffer:]
   ```

10. FLGPUOutput的子类将美化过的pixelBuffer叠加特效

   ```
   [GPUImageMovie processMovieFrame:withSampleTime:]
   ```

## 备注
1. FaceUnity美化：
  * FaceUnityCameraViewController 拍照页面美化
  * FaceUnityVideoPreviewController 预览界面美化（不使用模板）
  * FaceUnityVideoPreviewController+Template 预览界面美化（使用模板）
2. 相机类：
  * FLGPUCameraViewController GPU美颜
  * FLTemplateCameraViewController GPU美颜+模板
  * FaceUnityCameraViewController FaceUnity相机 
3. 预览类：
  * FLGPUVideoPreviewController GPU美颜
  * FLTemplatePreviewController GPU美颜+模板
  * FaceUnityVideoPreviewController FaceUnity预览
4. 调用FaceUnity的回调

  * FLGPUImageMovieDelegate(包括FLGPUImageMovie和FLGPUOutput)
    ```
    -(CVPixelBufferRef)processVideoPixelBuffer:(CVPixelBufferRef)pixelBuffer;
    ```

  * FLCameraManagerDelegate
    ```
    -(void)willOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer;
    
    -(CVPixelBufferRef)processVideoPixelBuffer:(CVPixelBufferRef)pixelBuffer;
    ```
    
  * FLOutputSourceDelegate
    ```
    -(void)willOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer;
    
    -(CVPixelBufferRef)processVideoPixelBuffer:(CVPixelBufferRef)pixelBuffer;
    ```

  * GPUImageVideoCameraDelegate 
    ```
    -(void)willOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer;
    ```
    
[^1]:与基础相机不同在于先初始化FaceUnity SDK，并将采集到的帧通过SDK进行美化
[^2]:原视频先通过重写FLGPUImageMovie的processMovieFrame方法调用FaceUnity美化，再叠加FLGPUOutput子类定制的特效
[^3]:原视频通过重写FLGPUImageMovie的processMovieFrame方法调用FaceUnity美化作为一个OutputSource源，模板作为另一个源，最后合并输出