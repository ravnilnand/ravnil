There are few benefits to use this repository for 64 bit architectures.

1. increase apk/aar size
2. increase memory usage
3. increate power consumption
4. become slow

But other dependancies of your project may include binaries for 64 bits architectures by some reason.
In that case, your app can not run on some devices by `java.lang.UnsatisfiedLinkError`.  
For example, your other dependancies include `arm64-v8a` but some other dependancies (including this repository) do not include `arm64-v8a` binaries and if you try to run on devices that CPU is 64 bits ARM architecture, your app crash when accessing native library.

In that case, there are several ways, one is of course building all for 64 bits architectures.  
The other is just exclude binaries for the architectures that you don't need (ex. `arm64-v8a`). You can do this using `android.packagingOptions` like as follows;

```
android {
	packagingOptions {
		exclude 'lib/armeabi/*.so'
		exclude 'lib/arm64-v8a/*.so'
		exclude 'lib/mips64/*.so'
		exclude 'lib/x86_64/*.so'
	}
}
```