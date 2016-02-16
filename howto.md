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

## Internal behavior
sorry this paragraph is not ready yet.  

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

## How to create Bitmap in IFrameCallback
I don't recommend this way because IFrameCallback is relatively slow but if you really need to create Bitmap in IFrameCallback, please refer following code.

``` java
    private final Bitmap bitmap = Bitmap.createBitmap(UVCCamera.DEFAULT_PREVIEW_WIDTH, UVCCamera.DEFAULT_PREVIEW_HEIGHT, Bitmap.Config.RGB_565);
    private final IFrameCallback mIFrameCallback = new IFrameCallback() {
    	@Override
    	public void onFrame(final ByteBuffer frame) {
    		frame.clear();
    		synchronized (bitmap) {
    			bitmap.copyPixelsFromBuffer(frame);
    		}
    		mImageView.post(mUpdateImageTask);
    	}
    };
    
    private final Runnable mUpdateImageTask = new Runnable() {
    	@Override
    	public void run() {
    		synchronized (bitmap) {
    			mImageView.setImageBitmap(bitmap);
     		}
    	}
    };
```  

## How to start app when user connect UVC device to Android device
If you want to start app automatically, add followings on your AndroidManifest.xml
```
	    <activity
            android:name="{FQN of your main activiy}"
            android:label="{app name}"
			android:launchMode="singleTask" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
			<intent-filter>
                <action android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED" />
            </intent-filter>
            <meta-data
                android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED"
                android:resource="@xml/device_filter" />
		</activity>
```

You can edit device_filter.xml if you want to use only specific UVC camera.

And request permission when app detects UVC devices.
```
	private final OnDeviceConnectListener mOnDeviceConnectListener = new OnDeviceConnectListener() {
		@Override
		public void onAttach(UsbDevice device) {
			// this is called when user connect UVC camera and Android detected it.
			// if you want to use camera automatically, request permission here like as follows.
			final int n = mUSBMonitor.getDeviceCount()
			if ((device == null) && (n > 0)) {
				// #onAttach will be called with null argument when USBMonitor detect device connection without intent.
				final List<UsbDevice> devices = mUSBMonitor.getDeviceList();
				// set first one
				device = devices.get(0);
			}
			if (n == 1) {
				// if there is only one camera, request permission.
				// first time you call #requestPermission or app has no permission, Android shows permission dialog.
				// if app already has permission or user give permission on permission dialog, USBMonitor call #onConnect callback method.
				final boolean result = mUSBMonitor.requestPermission(device);
				if (result) {
					// when failed. your device may not support USB.
				}
			} else if (n > 1) {
				// show dialog to select camera
//				CameraDialog.showDialog(this);
				// or if you know your device-id/product-id that you want to use and device is that one, request permission
			}
		}

		@Override
		public void onConnect(final UsbDevice device, final UsbControlBlock ctrlBlock, final boolean createNew) {
			// this is called user give permission. now you cane open camera and start previewing
//			mHandler.openCamera(ctrlBlock);
//			startPreview();
		}

		@Override
		public void onDisconnect(final UsbDevice device, final UsbControlBlock ctrlBlock) {
			// this is called when user close camera (and also called when user remove UVC camera while using)
//			mHandler.closeCamera();
		}

		@Override
		public void onDettach(final UsbDevice device) {
			// this is called when UVC camera removed from the device. (will be after #onDisconnect)
		}

		@Override
		public void onCancel(final UsbDevice device) {
			// this is be called when user did not give permission to app
		}
	};
```

## How to debug over network
If your device support network debugging, you can connect adb and debug via a local network(wifi).
Please try following steps;

1. Connect your device via USB cable.
2. run "adb tcpip 5555" on console.
3. run "adb connect xxx.xxx.xxx.xxx:5555" on console
xxx.xxx.xxx.xxx is local IP address of your device.
4. Disconnect USB cable.

If you can see message of "connect to xxx.xxx.xxx.xxx:5555" on step3, now you can debug via network(wifi).
If you could't see the message, unfortunately your device does not support network debugging...

Note: If you connect multiple devices at the same time (including both devices over USB and over network), you need to specify which device you want to connect over network on step 2. using `-s device-name` option.
```
adb -s device-name tcpip 5555
```