---
description: https://www.android.com/
---

# Android

## Notes

* Contrary to Linux distributions, which use GNU's LibC as their core runtime (`libc.so`), Android elects to use its own C-runtime library, which is called **Bionic**. Bionic is more lightweight than GLibC, and more efficient for Android's purposes, leaving out features deemed unnecessary or too complicated.
* Android flash storage is often partitioned into dozens of partitions, of which Android uses about 5. Of those, the user can only write to one partition - `/data` an no other. In fact, most are not even mounted during regular use.
* One of the chief concerns of mobile devices is that they must always be repairable, and so some type of "recovery mode" must be enabled on them. To allow for system recovery or upgrades, there must be some way to boot the system from a known "safe" copy of an operating system.
* The Flash Friendly File System (F2FS) is a log-structured file system, optimized for NAND flash. It boasts performance improvements over Ext4, especially in random write requests.
* The standard Android partitions are:
  * `boot` (bootimg): contains kernel and RAMdisk to boot by default
  * `cache` (ext4): used for updates and recovery
  * `recovery` (bootimg): contains kernel and RAMdisk to start system recovery
  * `system` (ext4): OS binaries and frameworks
  * `userdata` (ext4 or F2FS): user data and configuration
* Android's root file system is mounted from a RAM disk (the "initramfs"). Upon every boot, the boot loader (fastboot) loads the filesystem image from the boot partition onto RAM, and provides it for the kernel. The contents of the root filesystem include:
  * `default.prop`: "Additional default property" file, sourced by init to load system-wide properties
  * `fstab.hardware`: the filesystem mount table user by `fs_mgr` and `vold`
  * `init`: the binary launched by the kernel on startup as PID 1
  * `init[...].rc`: the configuration file(s) for `/init`. The main configuration file is always `/init.rc`
  * `sbin/`: contains critical binaries, which the system needs even if /system cannout be mounted
* The `/system` partition is the home of all Android components and is mounted read-only for two reasons:
  * stability: removes the chance of file system corruption, even if the device is powered-off abruptly
  * security: a read-only mount is another layer of defence to protect the Android system components from being tampered with
* The `/system` partition is for the most part the same on most devices, and contains:
  * `app`: contains the prebundled system applications from Google
  * `bin`: contains various binaries, daemons and shell commands
  * `build.prop`: file sourced by init to load build properties on boot
  * `framework`: contains the Android frameworks in `.jar` files, with their executable dex files optimized alongside them in `.odex` files
  * `lib[64]`: runtime libraries as native ELF shared object (`.so`) files
  * `lost+found`: empty unless the filesystem crashed, in which case it may contain unlinked inodes
  * `media`: alarm, notification, ringtone and UI-effect audio files
  * `priv-app`: privileged applications
  * `vendor`: vendor-specific files, if any
  * `xbin`: special purpose binaries, not needed for normal operations
* The `/system/bin` directory contains the native executables used by Android, which can be classified into five categories:
  * **Service binaries**: invoked by /init throughout the lifetime of the system
    * `app_process`: host process for apps. Zygote and all user apps are instances of this binary, which initializes the DalvikVM/ART
    * `applypatch`: used during OTA updates
    * `bootanimation`: plays the Android boot animation while the graphics subsystem (`surfaceflinger`) is loading - often customized by vendor
    * `dalvikvm`: starts an instance of the Dalvik Virtual Machine
    * `debuggerd`: generates tombstones from process crashes
    * `linker`: Android's runtime linker, required for binary loading
    * `mediaserver`: audio/video recording/playback
    * `sdcard`: daemon to manage SD-Cards
    * `sensorservice`: coordinates reading from various sensors
    * `servicemanager`: service locator and fulcrum for all binder related services
    * `surfaceflinger`: composes graphics surfaces and loads them onto the framebuffer
    * `vold`: volume daemon to mount/unmount filesystems
  * **Debugging tools**: native binaries left for debugging
    * `adb`: Android Debugger Bridge (client), essentially the same binary as the host
    * `atrace`: Android tracing tools, uses Linux `ftrace` to debug and trace execution
    * `dex2oat`: DEX to ART conversion tool, compiles the DEX file to device executable format (supersedes `dexopt`)
    * `dumpstate`: meta-tool combining several useful utilities for capturing a debug snapshot of system state
    * `dumpsys`: service dump utility, connects to Android services and requests their `Dump()` method
    * `e2fsck`/`fsck_msdos`/`fsck.f2fs`: filesystem checkers for Ext2/3/4, VFAT and F2FS, run automatically by the system before mounting filesystems
    * `logcat`: print the system logs to stdout, with optional filters
    * `perf`: extremely powerful profiling tool which uses the kernel's profiling support
    * `run-as`: run an application under specific AID
    * `screencap`: capture framebuffer to stdout or to a PNG file
    * `screenrecord`: record moves of device display
    * `screenshot`: same as `screencap`, with optional sound to play on screen shot
    * `service`: command line utility for interfacing with the `servicemanager`
    * `toybox`: Android's multi-call binary
  * **UN\*X commands**: left as a convenience for the shell user, they are package into the single binary `toybox`, which is an Android specific version of the `busybox` binary
  * **Dalvik upcall scripts**: allow the user to interact with the Dalvik runtime frameworks, mostly for debugging
    * am: interact with ActivityManager to start activities, fire intents and much more
    * bu: start backup
    * content: interface to Android's content providers
    * input: interact with InputManager to inject input events
    * monkey: run an APK with randomly generated input events
    * pm: interact with PackageManager to list/install/remove packages, list permissions, etc.
    * settings: get/set system settings
    * wm: interact with WindowManager to change display size/density, etc.
  * **Vendor specific binaries**: naturally vary with vendor, but are generally either services or debugging tools

## Resources

### Articles

* [The internals of Android APK build process](https://medium.com/androiddevnotes/the-internals-of-android-apk-build-process-article-5b68c385fb20) - Android Dev Notes

### Books

* [Android Internals: A Confectioners Cookbook](http://newandroidbook.com/) - Jonathan Levin

### Courses

* [Android Basics in Kotlin](https://developer.android.com/courses/android-basics-kotlin/course)

### GitHub Repositories

* [Awesome Jetpack Compose Android Apps](https://github.com/androiddevnotes/awesome-jetpack-compose-android-apps)
* [Better `adb shell`](https://github.com/matan-h/adb-shell)

### Subreddits

* [r/Android](https://www.reddit.com/r/Android/)
* [r/androiddev](https://www.reddit.com/r/androiddev/)

### Websites

* [Android](https://www.android.com/)
* [Android API Levels](https://apilevels.com/)
* [Android Developers](https://developer.android.com/)
* [Android Open Source Project](https://source.android.com/)
  * [APEX file format](https://source.android.com/docs/core/ota/apex)
  * [Configure ART](https://source.android.com/docs/core/runtime/configure)
  * [Mainline](https://source.android.com/docs/core/ota/modular-system)
  * [The Android App Bundle format](https://developer.android.com/guide/app-bundle/app-bundle-format)
