# buildSrc + cmake issue

This project demonstrates that consecutive builds in Android Studio are not as fast as they can be under the following circumstances:

* having a `buildSrc` directory (even with no code inside)
* using a module with externalNativeBuild and cmake (`nativelib` module in our case)
* using the "play" button of Android Studio to build and deploy the apk on a device.

If any of the previous 3 requirements is not met, the issue is not reproduced and the build is fast.


## STR

Open the project with Android Studio and go to *"settings->Build, Execution, Deployment->Compiler"* and place the following flags to the *"Command-line Options"* field: `--profile --debug`.

* Hit the play button to deploy the app on a device or emulator
* Hit the play button again
* Hit the play button again

## OBS

Go to `"build/reports/profile"` and notice the 3 `"profileXXX.html*` files. All of them have a size around 67 KB. Opening them and visiting the "Artifact Transforms" tab, reveals a lot of transformed artifacts .

Go to Android Studio's Build tab and find the line:

```
 Task ':buildSrc:compileKotlin' is not up-to-date because:
  Value of input property 'filteredArgumentsMap' has changed for task ':buildSrc:compileKotlin'
```

The build time of the second and third run is not quite fast (takes around 2 seconds).

## EXP

The `"profileXXX.html*` created in the second or third consecutive run should be around 9 KB and the "Artifact Transforms" should contain no artifacts. 

The build should also be much faster (some ms).

## Workaround

In order to see the expected behavior you can either delete the whole "buildSrc" folder or comment out the "cmake" block in "nativelib\build.gradle".

A ticket has been created for this issue: https://github.com/gradle/gradle/issues/19811


