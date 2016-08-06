Google already stop developing plugin for Eclipse now and I don't use eclipse for Android development now(and this repository also already converted to project for Android Studio).
So this is just my assuming,

1. make new Eclipse project
2. manually copy all Java sources into your project with their directories.
3. manually copy all shared libraries(.so) into you project.

As I tested, you can also import as gradle project and run all tasks in Eclipse but I don't recommend because we can't sourcecode-debug as Android app.

1. add `local.properties` file and write `sdk.dir` and `ndk.dir` with your path.
2. edit `gradle-wrapper.properties` file under {UVCCamera}/gradle/wrapper as follows;  
    ```
    distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
    ```  
3. import all under `UVCCamera` as **gradle project**.
4. if you see dialog to confirm whether you want to override existing eclipse project descriptors, you should `Override` (instead of `Kepp`) otherwise you can see errors in editor.
5. Now you can execute all gradle tasks in Eclipse.