## How to hold permanent permission

You may want to give permanent permission to your app.  
To Holding a permanent permission for USB, you need to add intent filter to `activity` section on`AndroidManifest.xml`. Additionally, it is better to add `launchMode` in `activity` section as `singleTask ` or`singleInstance`(I usually use `singleTask `).
(Regarding details about `launchMode` and why it is better to set as this, please check Android Developer site.)
```
<activity
	android:launchMode="singleTask"
	...
	<intent-filter>
		<action android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED" />
		<meta-data
			android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED"
			android:resource="@xml/device_filter" />
	</intent-filter>
	...
```

Optionally, you can also add followings;
```
	<intent-filter>
		<action android:name="android.hardware.usb.action.USB_DEVICE_DETACHED" />
	</intent-filter>
```

You can see this on `AndroidManifest.xml` of UsbCameraTest4 sample project.
If there is above intent filter in `AndroidManifest.xml` and once user choose the app as default app(always use the app for the device), Android system automatically give a permission to the Activity every time connecting the USB device (and you can not select other similar app). You can clear this setting on `Apps` setting.
You can also add `USB_DEVICE_DETACHED` intent filter to your `AndroidManifest.xml`, but some Android devices with some Android version can not handle this intent filter correctly.  
Please mind that adding above intent filter let app accept all connecting event of UVC camera. This means when connect UVC camera to your device and the app does not have permission for the device, Android system always ask you permission. If you only need specific UVC camera, please add the vendor id and product id to `device_filter.xml`

Then first time when you connect your camera to your device, Android System ask whether you want to use the app as default app. 
When app starts, you need to check connected USB device and request permission using USBMonitor#requestPermission.
If your app already has permission to the USB device, OnDeviceConnectListener#onConnect is called immediately and you can do as usual.
If your app does not have permission, Android System shows permission dialog. After user give permission,  OnDeviceConnectListener#onConnect is called.
If user does not give permission, OnDeviceConnectListener#onCancel is called.

Note: Unfortunately this mechanism only work for `Activity` and not work for `Service` on current Android implementation.
