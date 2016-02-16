## How to change resolution
(This is old. Latest version supports preview resolution setting by UVCCamera#setPreviewSize)

If you want just one higher resolution than 640×480, for example 1920×1080, all you have to do is,

1 ) Change DEFAULT_PREVIEW_WIDTH macro from 640 to 1920 in UVCPreview.h  
2 ) Change DEFAULT_PREVIEW_HEIGHT macro from 480 to 1080 in UVCPreview.h  
3 ) Rebuild shared libraries using Android NDK  
4 ) Replace shared libraries placed {project home directory}/libs/armeabi-v7a in specific sample project.  
5 ) Some projects need to set aspect ratio of camera images. It is like this,  
``` java
    from

    mCameraView.set AspectRatio(640 / 480.f);

    to

    mCameraView.setAspectRatio(1920 / 1080.f);
```
6 ) Run app or export as apk.  
