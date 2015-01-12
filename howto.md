# This is How To of UVCCamera project

## How to change resolution

If you want just one higher resolution than 640×480, for example 1920×1080, all you have to do is,

1. Change DEFAULT_PREVIEW_WIDTH from 640 to 1920 in UVCPreview.h  
2. Change DEFAULT_PREVIEW_HEIGHT from 480 to 1080 in UVCPreview.h  
3. Rebuild shared libraries using Android NDK  
4. Replace shared libraries placed {project home directory}/libs/armeabi-v7a in specific sample project.  
5. Some projects need to set aspect ratio of camera images.  
    * It is like this,  
    * from `mCameraView.set AspectRatio(640 / 480.f);`   
    * to  `mCameraView.setAspectRatio(1920 / 1080.f);`  
6. Run app or export as apk.  

## How to use MJPEG mode

If you want to use MJPEG mode, please change as follows,

1. in UVCPreview::UVCPreview function (this is just safety)  
    * from  
        `frameBytes(DEFAULT_PREVIEW_WIDTH * DEFAULT_PREVIEW_HEIGHT * 2)`
    * to  
        `frameBytes(DEFAULT_PREVIEW_WIDTH * DEFAULT_PREVIEW_HEIGHT * 4)`

2. in UVCPreview::prepare_preview function  
    * from  
        `   result = uvc_get_stream_ctrl_format_size_fps(mDeviceHandle, ctrl,  
                  UVC_FRAME_FORMAT_YUYV,  
                  requestWidth, requestHeight, 1, 30 );`
    * to  
        `    result = uvc_get_stream_ctrl_format_size_fps(mDeviceHandle, ctrl,
                  UVC_FRAME_FORMAT_MJPEG,
                  requestWidth, 1, 30 );`

3. in UVCPreview::do_preview function,  
* from
    `   while (LIKELY(isRunning())) {
              frame = waitPreviewFrame();
              if (LIKELY(frame)) {
                  frame = draw_preview_one(frame, &mPreviewWindow, uvc_any2rgbx, 4);
                  addCaptureFrame(frame);
              }
          }`
* to  
`    while (LIKELY(isRunning())) {
        frame_mjpeg = waitPreviewFrame();
        if (LIKELY(frame_mjpeg)) {
            frame_yuyv = uvc_allocate_frame(frame_mjpeg->width * frame_mjpeg->height * 2);
            result = uvc_mjpeg2yuyv(frame_mjpeg, frame_yuyv);	// MJPEG => yuyv
            uvc_free_frame(frame_mjpeg);
            if (LIKELY(!result)) {
                frame_yuyv = draw_preview_one(frame_yuyv, &mPreviewWindow, uvc_any2rgbx, 4);
                addCaptureFrame(frame_yuyv);
            } else {
                uvc_free_frame(frame_yuyv);
            }
        }
    }`  

of course you need to add “frame_mjpeg” and “frame_yuyv” variables instead of “frame” variavle.