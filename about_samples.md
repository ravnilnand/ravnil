## About samples

There are 8 samples are available now.  

Note: Unless you actually need and you know what you should do, please do not change class(file) name and field name of UVCCamera.java otherwise app will crash when accessing USB camera function.

1 ) USBCameraTest0  
This is most simple project that only show how to start/stop previewing using SurfaceView.  

2 ) USBCameraTest  
This is most simple project that only show how to start/stop previewing. This is almost same as USBCameraTest0, but use customized TextureView to show camera images instead of using SurfaceView.  

3 ) USBCameraTest2  
This is sample project that show how to record video from UVC camera(without audio) as .MP4 file using MediaCodec encoder. This sample requires API>=18 because MediaMuxer is only supported API>=18.  

4 ) USBCameraTest3  
This is sample project that show how to record video(from UVC camera) with audio(from internal mic) as .MP4 file. This also shows several ways to capture still image.  This sample may most useful as base project of your customized app.  

5 ) USBCameraTest4  
This sample shows the way to access UVC camera and save video images on background service. This is one of the most complex sample because this requires IPC using AIDL.  

6 ) USBCameraTest5  
This is almost same as USBCameraTest3 but save video images using IFrameCallback interface instead of using input Surface from MediaCodec encoder.  
In most case, you should not use IFrameCallback to save images because IFrameCallback is much slower than using Surface. But IFrameCallback will be useful if you want to get video frame data and process them by yourself or passing them to other external library as byte buffer.  

7 ) USBCameraTest6  
This shows how to split video images to multiple Surface. You can see video images side by side view on this app. This sample also show how to use EGL to render image. If you want to show video images after adding visual effect/filter effects, this sample may help you.  


8 ) USBCameraTest7  
This shows how to use two camera and show video images from each camera side-by side. This is still experimental and may have some issue.

If you want to build above sample project, just import the project to your IDE and run it. You don't need `libUVCCamera` library project to build sample unless you want to change/modify/add some features in JNI side.  
