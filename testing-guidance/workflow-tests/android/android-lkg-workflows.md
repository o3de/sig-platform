# Android LKG Workflow Tests

Testing in this area should focus on the different ways O3DE can be built and deployed into an android device and the launch of a default level successfully.

## General Android Docs
* [O3DE User Guide: Android Support for O3DE](https://www.o3de.org/docs/user-guide/platforms/android/)

## Common Issues to Watch For

- Binary and assets are located at their correct place for each packaging mode.
- The most common packaging mode used during Android development is VFS (Virtual File System), be aware of network connection errors from the mobile device to Asset Processor running on PC.
- Rendering artifacts when running default level on Android devices.
- Low framerates when running default level on Android devices. Look out for spamming error/warnings in the Android log.
- Asserts and Errors that appear in the Android Log while running default level.
- Immediate crashes when launching the app on the Android device.
- Crashes that occur after some time leaving the app running.

## Workflows

### Area: Build android in different packaging modes

**Project Requirements**
* Create a new default project.
* Create `<project-path>/user/Registry/AssetProcessor.setreg` file to generate android assets in the cache:
````
{
    "Amazon": {
        "AssetProcessor": {
            "Settings": {
                "Platforms": {
                    "android": "enabled"
                }
            }
        }
    }
}
````
* Make sure `<project-path>/Platform/Android/android_project.json` exists and that it has android settings section in it. Example:
````
"android_settings": {
    "package_name": "org.o3de.DefaultProject",
    "version_number": 1,
    "version_name": "1.0.0",
    "orientation": "landscape"
},
````
* Build the default project.
* Open editor and create a default level.
* Create `<project-path>/Registry/autoexec.game.setreg` file to set the starting level:
````
{
    "O3DE": {
        "Autoexec": {
            "ConsoleCommands": {
                "LoadLevel": "DefaultLevel"
            }
        }
    }
}
````
* Run Asset Processor and wait until all assets have been processed.

**Editor Platforms:**
* Windows, Linux, Mac

**Game Launcher Supported Platforms:**
* Android

**Product:** Proven that O3DE can be built on Android in different packaging modes and generate the right APK content and assets on each one.

**Suggested Time Box:** 1 hour for requirements + 30 minutes per packaging mode.

| Workflow                      | Requests                                                                                                                                                                                                                       | Things to Watch For                                                                                                                                                                                                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Assets in APK**   | <ol><li>Uninstall the default project's app from device (if previously installed).</li><li>Follow the steps from https://www.o3de.org/docs/user-guide/platforms/android/generating_android_project_windows/ to build and deploy with the following alterations:<ol type="a"><li>Use the default project created instead of `o3de-atom-sampleviewer`</li><li>Use `o3de-build/android_apk` for `%O3DE_BUILD_ROOT%` instead of `o3de-build`</li><li>When running `generate_android_project.py` command use `LOOSE` for `%O3DE_ANDROID_ASSET_MODE%`</li><li>When running `deploy_android.py` command use `APK` for `%O3DE_ANDROID_DEPLOY_TYPE%`</li></ol></ol> | <ul><li>Binaries will be inside the android APK package. Unzip `O3DE_BUILD_ROOT/app/build/outputs/apk/profile/app-profile.apk` and verify `lib/arm64-v8a` folder contains `.so`.</li><li>Assets will be inside the android APK package. Unzip `O3DE_BUILD_ROOT/app/build/outputs/apk/profile/app-profile.apk` and verify `assets` folder contains all the assets from `<project-path>/Cache/android`.</li><li>Execution's log and user folder will be generated in device's sdcard (`/sdcard/Android/data/com.o3de.DefaultProject/files/user/`).</li></ul> |
| **Assets in sdcard**   | <ol><li>Uninstall the default project's app from device (if previously installed).</li><li>Follow the steps from https://www.o3de.org/docs/user-guide/platforms/android/generating_android_project_windows/ to build and deploy with the following alterations:<ol type="a"><li>Use the default project created instead of `o3de-atom-sampleviewer`</li><li>Use `o3de-build/android_sdcard` for `%O3DE_BUILD_ROOT%` instead of `o3de-build`</li><li>When running `generate_android_project.py` command use `LOOSE` for `%O3DE_ANDROID_ASSET_MODE%` and remove `--include-apk-assets`</li><li>When running `deploy_android.py` command use `BOTH` for `%O3DE_ANDROID_DEPLOY_TYPE%`</li></ol></ol> | <ul><li>Binaries will be inside the android APK package. Unzip `O3DE_BUILD_ROOT/app/build/outputs/apk/profile/app-profile.apk` and verify `lib/arm64-v8a` folder contains `.so`.</li><li>Assets will be inside the device's sdcard (not in the APK package). Go to `/sdcard/Android/data/com.o3de.DefaultProject/files` and verify it contains all the assets from `<project-path>/Cache/android`.</li><li>Execution's log and user folder will be generated in device's sdcard (`/sdcard/Android/data/com.o3de.DefaultProject/files/user/`).</li></ul> |
| **Assets by VFS (minimum assets in sdcard)**   | <ol><li>Uninstall the default project's app from device (if previously installed).</li><li>Create `<project-path>/user/Registry/bootstrap.setreg` file to enable remote filesystem on Android:<br><pre>{<br>    "Amazon": {<br>        "AzCore": {<br>            "Bootstrap": {<br>                "android_remote_filesystem": 1,<br>                "android_connect_to_remote": 1,<br>                "android_wait_for_connect": 1,<br>            }<br>        }<br>    }<br>}</pre></li><li>Follow the steps from https://www.o3de.org/docs/user-guide/platforms/android/generating_android_project_windows/ to build and deploy with the following alterations:<ol type="a"><li>Use the default project created instead of `o3de-atom-sampleviewer`</li><li>Use `o3de-build/android_vfs` for `%O3DE_BUILD_ROOT%` instead of `o3de-build`</li><li>When running `generate_android_project.py` command use `VFS` for `%O3DE_ANDROID_ASSET_MODE%` and remove `--include-apk-assets`</li><li>When running `deploy_android.py` command use `BOTH` for `%O3DE_ANDROID_DEPLOY_TYPE%`</li></ol><li>With the mobile device connected to PC run this command from the console: `adb reverse tcp:45643 tcp:45643` <p>**IMPORTANT**: This command has to be run every time the adb connection is lost or killed, which is each time the cable is plugged, but also it can happen often without knowing. If when running the app it shows refuse-connections messages in the log, that's a signal that adb reverse command needs to be run again before running the app.</p></li></ol> | <ul><li>Binaries will be inside the android APK package. Unzip `O3DE_BUILD_ROOT/app/build/outputs/apk/profile/app-profile.apk` and verify `lib/arm64-v8a` folder contains `.so`.</li><li>Minimum assets necessary to launch the app will be inside the device's sdcard (not in the APK package). Go to `/sdcard/Android/data/com.o3de.DefaultProject/files` to check they are there. Minimum assets are:<ol type="a"><li>`engine.json` file.</li><li>Bootstrap `.setreg` files.</li><li>Config files inside `config` folder.</li></ol></li><li>All the assets will be obtained via network connecting to the PC and read from `<project-path>/Cache/android/`.</li><li>Execution's log and user folder will be generated in PC (`<project-path>/user/`).</li></ul> |
---

### Area: Run default level on Android device

**Project Requirements**
* You've built android in all the different packaging modes.

**Game Launcher Supported Platforms:**
* Android

**Product:** A default level is launched correctly on android devices on each packaging mode.

**Suggested Time Box:** 5 minutes per packaging mode.

| Workflow                      | Requests                                                                                                                                                                                                                       | Things to Watch For                                                                                                                                                                                                                                                 |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Run default level** | <ol><li>Follow the build steps for one packaging mode from the previous area in this document.</li><li>Launch the default project's app in the android device.</li><li>Use Android Studio (or any other method) to check the log output from the default project's app. Select the `O3DE_BUILD_ROOT` folder when opening Android Studio to open the project and after all the processing is done the Logcat tool window will be available and once the app is running on the device you can filter the output by app.</li><li>Do the same with another packaging mode.</li></ol> | <ul><li>The level opens successfully</li><li>It doesn't crash after a while (for example a couple of minutes).</li><li> The level is rendered correctly without artifacts.</li><li>Framerate is 15fps or above. This depends on the device used, but the key thing to look for is that there are no spamming errors or warnings in the log indicating that something wrong is happening every frame.</li></ul> |
---
