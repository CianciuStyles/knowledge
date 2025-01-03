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
    * `am`: interact with ActivityManager to start activities, fire intents and much more
    * `bu`: start backup
    * `content`: interface to Android's content providers
    * `input`: interact with InputManager to inject input events
    * `monkey`: run an APK with randomly generated input events
    * `pm`: interact with PackageManager to list/install/remove packages, list permissions, etc.
    * `settings`: get/set system settings
    * `wm`: interact with WindowManager to change display size/density, etc.
  * **Vendor specific binaries**: naturally vary with vendor, but are generally either services or debugging tools
* The `/system/xbin` directory is akin to Unix's `/sbin` in the sense that it contains binaries which administrators find useful, but normal users are probably better off staying away from. The "x" was chosen to avoid confusion with Android's own `/sbin`, which is part of the root filesystem. It contains:
  * `add-property-tag`: add properties to a system .prop file
  * `daemonize`: turn an executable into a daemon by running in background and closing stdin/stdout/stderr
  * `dexdump`: DEX file dumping tool, provides header and bytecode dump
  * `nc`: Netcat, the swiss army knife of TCP and UDP
  * `sqlite3`: SQLite 3 command line tool
  * `strace`: system call tracer using the Linux ptrace system call, powerful tool for tracing and reverse engineering
  * `su`: switch user (to root or other)
  * `tcpdump`: packet capture tool
* The `/system/lib` and `/system/lib64` directories contain the shared libraries used by the binaries in `/system/bin` and `/system/xbin`. Common subdirectories are:
  * `drm`: providing DRM engines
  * `egl`: for Android's OpenGLES implementation
  * `hw`: containing HAL modules
  * `ssl/engines`: allowing OpenSSL intergration with Android Keystore mechanism
* Nearly all of Android's binaries are dynamically linked - an exception to the rule are the binaries in `/sbin`
* Much like its UN\*X namesake, Android's `/system/etc` contains miscellaneous configuration file (`et cetera`) - contents commonly found in this directory include:
  * `bluetooth/`: the BlueDroid configuration files
  * `event-log-tags`: log tags for various Android system components
  * `gps.conf`: GPS configuration file
  * `hosts`: hosts map
  * `permissions/`: XML files containing permissions for built-in apps, used by the PackageManager
  * `security/`: contains the device's hard coded certificate authorities (`cacerts/`), OTA update certificates (`otacerts.zip`) and SELinux labels for signed APKs
* The `/data` partition is where all the user's personal data resides. The main advantages for having a separate partitions are:
  * `/data` is decoupled from the underlying Android OS version: system upgrade and recovery can wipe and rewrite the entire `/system` partition without affecting the user's data
  * `/data` may be encrypted if the user requires it
  * `/data` may also be made non-executable: this would greatly mitigate an attack vector for malware
* The contents of the `/data` partition include:
  * `anr`: used by dumpstate to record stack traces of non-responsive Android apps
  * `app`: user-installed applications
  * `app-lib`: JNI libaries of applications (both system and user-installed)
  * `bugreports`: used exclusively by `bugreport` for generated reports
  * `dalvik-cache`: the optimized `classes.dex` of system and user applications
  * `data`: data directories for installed applications - this is the only location in the entire filesystem which is writable by apps
  * `local`: a readable/writable temporary directory for uid shell (usable in ADB sessions)
  * `lost+found`: empty unless the filesystem crashed, in which case it may contain unlinked inodes
  * `misc`: miscellaneous data and configuration directories for components
  * `media`: used by the `sdcard` service for mounted media
  * `property`: contains persistent properties (i.e., saved across device reboots)
  * `system`: a multitude of system configuration files
  * `tombstones`: application crash reports generated by `debuggerd`
  * `user`: provides "multi-user" capabilities, symlinking user numbers to directories with installed applications and data for those users
* One of Android's strongest features is its built-in support for SD Cards, which are usually formatted with the vfat or fat32 filesystems - because those filesystems do not support permissions, Android resorts to _emulating_ the sdcards via FUSE (Filesystem in USEr mode). The mount point for the SD card is usually `/storage/ext_sd`
* Android's "Secure Storage" feature, commonly referred to as **asec**, provides a mechanism for applications to securely deploy onto the device while maintaining a reasonable level of assurance that the user will not copy them to another device. Asec **containers** are, in essence, encrypted filesystem images which begin with a fixed header. Asec creation and management is handled by the volume manager `vold`.
* The system images are provided by the vendor and are meant to be flashed as the "factory default" distribution of Android onto a device. The images are comprised of several files:
  * Boot loader: responsible for finding and loading the boot image, handling firmware updates, and booting into recovery mode. Most boot loaders implement a small USB stack over which they can communicate with the host to control the boot or update process
  * Boot image: normally consists of the kernel and a RAM disk, and is used to load the system. The RAIM disk will serve as the root filesystem for Android, an its /init.rc will provide directives as to how to load the rest of the system partitions
  * Recovery image: consists of the kernel and a (different) RAM disk, and is used to load the system into "Recovery mode"
  * System partition: this is the full Android system
  * Data partition: contains the "factory default" data files, which support the binaries in the system partition
* Android vendors are free to implement their own boot loaders, though most (Samsung being a notable exception) choose the LK (little kernel) boot loader. LK concerns itself with only the minimal functions, which include:
  * basic hardware support
  * finding and booting the kernel: locating the bootimg and parsing its components (kernel image, ramdisk and device tree)
  * basic UI
  * USB target support
  * flash partition support: enable the boot loader to erase or overwrite partitions, as required during upgrade or recovery
  * digital signature support: to provide support for loading digitally signed images with SSL certificates
* The boot loader image is comprised of several sub-images, each of which is meant to be flashed to a specific partition. The boot loader itself is in "aboot", which is the Application Processor Boot loader.
* The boot loader on Android devices is usually **locked**, meaning it will refuse to flash or boot updates which are not digitally signed. Depending on the vendor, a user may or may not be able to unlock the phone.
* The Linux kernel, unlike most OS kernels, is mostly compressed: the kernel file format, known as `zImage`, consists of self-extracting code, which unpacks the rest of the kernel image in memory. The kernel is the most architecture specific component of Android: whereas other components only care about the processor type, the kernel is also concerned with the board type and specific chipsets.
* The second component of the boot or recovery image is the initial RAM disk, often referred to as the `initrd`. This provides an initial filesystem, used as the `rootfs` when booting up the OS, and it's pre-loaded by the boot loader into the RAM alongside the kernel.
* Once the RAM Disk operation is done, Linux normally discards it in favor of the on-disk filesystem - in Android, however, the `initramfs` is kept in memory, and provides the root filesystem.
* Most Android bootloaders support the "FastBoot" protocol, which Google makes available as part of Android itself. The FastBoot protocol is a simple, text-based protocol which is meant to be used over a USB channel between the device and the host.
* System recovery and updates are similar processes: in both, the system needs to be diverted to an alternate boot sequence which loads a minimal configuration. Either process is normally started when the system is fully booted and in UI mode, though the device can also be ordered into recovery through `adb reboot recovery` or via `fastboot`.
* The update must be digitally signed, and is validated against certificates taken from `/system/etc/security/otacerts.zip` keystore - if the validation passes, the update is copied to the `/cache` partition.
* Occasionally, the vendor or the carrier (and sometimes Google itself) may provide an update to the Android OS in the form of an Over-The-Air (OTA) update, which are packaged as a single zip file, digitally signed, which consists of:
  * multiple patch files: this is to keep the update as small as possible
  * a patch binary: usually called `update-binary`, which can parse the patch files and apply them
  * a patch script: usually called `updater-script`, which executes the binary multiple times (one per patch) and specifies the expected hash of the file pre- and post-patch
  * an otacert file: which can be compared and optionally imported into `/system/etc/security/otacerts.zip`

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
