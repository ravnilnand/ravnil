## How to use MJPEG mode
(This is old. Latest version supports preview mode setting by UVCCamera#setPreviewSize. All samples try to use MJPEG mode first. If it failed, try YUV mode automatically.)

If you want to use MJPEG mode, please change as follows,

1 ) in UVCPreview::UVCPreview function (this is just safety)  
``` java
    from

    frameBytes(DEFAULT_PREVIEW_WIDTH * DEFAULT_PREVIEW_HEIGHT * 2)

    to

    frameBytes(DEFAULT_PREVIEW_WIDTH * DEFAULT_PREVIEW_HEIGHT * 4)
```
2 ) in UVCPreview::prepare_preview function  
``` java
    from

    result = uvc_get_stream_ctrl_format_size_fps(mDeviceHandle, ctrl,
        UVC_FRAME_FORMAT_YUYV,  
        requestWidth, requestHeight, 1, 30 );

    to  

    result = uvc_get_stream_ctrl_format_size_fps(mDeviceHandle, ctrl,
        UVC_FRAME_FORMAT_MJPEG,
        requestWidth, 1, 30 );  
```
3 ) in UVCPreview::do_preview function,  
``` java
    from  

    while (LIKELY(isRunning())) {
        frame = waitPreviewFrame();
        if (LIKELY(frame)) {
            frame = draw_preview_one(frame, &mPreviewWindow, uvc_any2rgbx, 4);
            addCaptureFrame(frame);
        }
    }

    to  

    while (LIKELY(isRunning())) {
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
    }
```  
of course you need to add “frame_mjpeg” and “frame_yuyv” variables instead of “frame” variavle.
