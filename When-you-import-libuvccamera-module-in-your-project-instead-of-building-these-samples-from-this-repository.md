Don't forget to add dependency to [libcommon](https://github.com/saki4510t/libcommon) repository.

Your top level `build.gradle` should include `maven { url 'http://raw.github.com/saki4510t/libcommon/master/repository/' }` in `repositories` in `allprojects` section

```
allprojects {
    repositories {
        maven { url 'http://raw.github.com/saki4510t/libcommon/master/repository/' }
        jcenter()
    }
}
```
and `commonLibVersion= '1.4.3'` in `ext` section.
```
...
ext {
    ...
    commonLibVersion= '1.4.3'
    ...
}
```

`build.gradle` of `usbCameraCommon` module should include

```
compile("com.serenegiant:common:${commonLibVersion}") {
	exclude module: 'support-v4'
}
```

If you don't include `usbCameraCommon` module in your project, please add above in you application module(that you want to use `libuvccamera`).

After that, please sync your gradle project, then gradle will automatically download it from Github.