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
* The Android `init` is vastly different than that of UN\*X or Linux, with the most important differences being in its support of System Properties and using a particular set of `rc` files.
* The Android System Properties provide a globally accessible repository of configuration settings. Because `init` is the ancestor of all processes in the system, it is only natural that it implements the property store.
* `init` recognizes several special prefixes, which govern how it handles the properties:
  * The `persist` pseudo-prefix: designates the property as meant to survive reboot
  * The `ro` pseudo-prefix: is used for "read-only" properties
  * The `ctl` prefix: is used to provide a convenient way to control init's services
* The `rc` files are composed of **trigger** and **service** blocks. Trigger blocks contain commands, to be executed when a trigger is satisfied; service blocks define daemons, which `init` can start by command and be responsible for.
* `init` can also be filling additional roles - that of `ueventd` and `watchdogd`. `ueventd` assumes the responsibility of managing hardware devices: responding to kernel notifications and device representations in the `/sys` filesystem, and making them available to processes via symbolic links in `/dev`. `watchdogd` is responsible for interfacing with the hardware watchdog timer, by setting a timeout value and sending a keepalive signal at regular intervals.
* The services in the "core" class are the first to be started during user-mode boot. These services do not access the `/data` partition, and therefor can run irrespective of whether or not it is mounted. These services are:
  * `adbd`:  it's the device daemon providing the server functionality of ADB, and is run by default as uid root
  * `servicemanager`: key component of Android's IPC mechanism, especially for its function as a service mapper
  * `healthd`: meant to service general "device health" tasks periodically (e.g., querying and displaying information about battery)
  * `lmkd`: provides an interface to the kernel's Low Memory Killer (lmk), which is an Androidism to automatically kill tasks during memory pressure
  * `logd`: this daemon serves as a centralized user-mode logger and provides log pruning
  * `vold`: volume-management daemon that automatically mounts file systems ("volumes") as they are detected by the kernel
* Among the "network" services we have:
  * `netd`: dedicated daemon to control network interfaces and configuration management
  * `mtpd`: daemone responsible for PPP and L2TP (but not IPSec) - not related to the Media Transfer Protocol at all!
  * `racoon`: de facto stardard VPN daemon
  * `rild`: Radio Interface Layer Daemon, provides virtually all the telephony capabilities by interfacing with the baseband
* Among the "graphics and media" services we have:
  * `surfaceflinger`: provides the heart of the Android Graphics Stack - a flinger is a component which merges one or more layers of input into a single layer of output
  * `bootanimation`: exclusively used by `surfaceflinger` as a placeholder while it is loading, so it has to rely on low level OpenGL and SKIA calls
  * `mediaserver`: serves as a focal point for multimedia handling, controlling both playback and recording, and is just a container for the actual services (AudioFlinger, CameraService, MediaPlayerService)
  * `drmserver`: provides Digital Rights Management (DRM) services for copy-protected content
* Among the "other" services we have:
  * `installd`: responsible for package installation and removal - the daemon is passive, listening on a socket set up by init, over which commands generated by the Android framework are delivered
  * `keystore`: provides a storage service for keys (in theory, any arbitrary name-valule pairs)
  * `debuggerd[64]`: normally dormant until an application crashes, and creates a tombstone (crash report containing a small memory dump)
  * `sdcard`: provides the user-mode support for SDCards, including enforcement of permission on the otherwise permission-less FAT filesystem
  * `zygote[64]`: provides the core support for all of the Android Framework Runtime services, in the form of an initialized empty Dalvik Virtual Machine, stopped just before loading the main class - the "true name" of the service is `app_process` and it's the process from which all other app processes are created by forking
* The rationale behind the Zygote process is twofold:
  * application startup time is greatly decreased, as Zygote does the deterministric initialization that all virtual machines require, leaving the forked process to only load the required classes
  * memory sharing is optimized: because all virtual machines fork from Zygote, they can take advantage of implicit memory sharing performed by the kernel
* If any application or system component needs to use another service, it must first consult the `servicemanager` to get a handle. Similarly, services cannot expect clients until they register their presence with it.
* Services are automatically removed from `servicemanager` when their processes die, as Binder can detect that and send a death notification.
* Android's framework services are implemented in `system_server` threads. Applications thus need to rely on Inter-Process Communication (IPC) in order to invoke them. This is where the _Binder_, Android's proprietary IPC mechanism, comes into play.
* Binder is a Remote Procedure Call mechanism, allowing applications to communicate programmatically, but without having to worry about how to locate the service process, send and receive messages and objects, and support credentials.
* A Binder message is referred to as a `Parcel`.
* The Android Interface Definition Language (AIDL) is essentially a derivative of Java which is used to specify methods and objects that are serializable as Parcels and can be transmitted through Binder.
* The vast majority of framework services are simple enough that they can run as threads, and `system_server` is host process where the service threads are running in.
* The `system_server` is not a native app: it is implemented mostly in Java with some JNI calls in places where it must start native services. The services are similarly implemented in Java, and many on them rely on JNI to escape the virtual machine and interact with hardware components.
* Once the services are started, SystemServer has nothing more to do in its main thread, so it enters a loop where it polls its file descriptors (especially the Binder handle) for incoming messages: when they arrive, they are dispatched to their respective targets.
* The files within the `/proc` filesystem are not really there until you ask for them - this means that every time you display the files you are likely to get different content. This makes the `/proc` filesystem an extremely powerful mechanism for system and process tracing, provided the method used is that of polling (keep on explicitly asking for specific entries periodically).
* In the `/proc` filesystem there are three symbolic links, which are:
  * `cwd`: displays the current working directory
  * `exe`: displays the full path to the executable used to start this process
  * `root`: displays the root directory (usually `/`, unless an application is `chroot`-ed)
* A process performs all of its I/O through **file descriptor** - the files, pipes and socket opened map to numbered file descriptors, with three default ones (`stdin` with number 0, `stdout` with number 1, and `stderr` with number 2).  For a running process, the open file descriptors can be found in `/proc/$pid/fd`.
* At first glance, the `fdinfo` directory looks just like `fd`, but without symbolic links - instead, for every open file descriptor, the corresponding `fdinfo/##` entry holds metadata about the file, including:
  * the flags used in the open system call
  * the current position of the "file pointer"
* The `status` proc entry is a one-stop shop for all the things you'd want to know at a high level on the process being inspected - this is effectively a human-readable dump of the `task_struct` structure in the Linux kernel
* `pid` does **not** stand for Process ID, as it described the **thread** and not the process ID of the entry being inspected. A process is, therefore, a group of threads sharing the same resources (virtual memory, file descriptors, etc.) and that is shown by the `Tgid` field. The `Tgid` and the `pid` will match in the case of the main thread of a process.
* To calculate memory statistics, one can take into account the following simple formula:\
  \
  `VmSize = VmRSS + VmFileMapped + VmSwap + VmLazy`\
  \
  where:
  * VmRSS (Resident Set Size): pages of virtual memory which are presently backed by physical RAM pages (because they have been recently active, or the process/kernel requires them to be locked in memory)
  * VmFileMapped: pages which were retrieved from files (by means of the `mmap` system call) and that may be written back to the files at any time to free memory
  * VmSwap: pages which resulted from memory allocation (i.e., `malloc`) and do not have any file backing (also called anonymous pages) - in Linux they might be saved in the `swap` partition, but Android does not have it, so this value is almost always 0
  * VmLazy: pages that the kernel allocate lazily, as programmers often allocate far more memory than actually needed
* Summing up all VmRSS for all processes in order to calculate the overall RAM footprint would be wrong, as some of the resident memory might be shared with other processes. Linux offers a more accurate measure called **Proportional Set Size (PSS)**.
* The LowMemoryKiller (`lmk`) is an Androidism which enhances the Linux Out-Of-Memory (OOM) mechanism by pre-emptively killing processes before a real OOM condition is triggered.
* The `strace` binary is an utterly invaluable tracing tool as it's able to understand the system calls and its arguments requested from a process.
* Android relies on the facilities of Linux for its basic security needs. For most apps, however, an additional layer of security is enforced by the Dalvik Virtual Machine. Android Security is therefore an amalgam of the two approaches which allows for defense in depth.
* By default, applications are given a minimal set of permissions, but are otherwise restricted. The minimal set, however, does not include anything which might be potentially sensitive - even if it is vital. For this reason, any permission outside the minimal set must be explicitly requested by the application, in its manifest.
* Android takes the classic permissions model obtained for free from the underlying Linux system and naturally employs it, but offers a novel interpretation: the "users" are granted to individual applications, not human users. A user cannot access another user's files, directories, or processes - and this exact isolation enables applications to run alongside each other, but with no power to influence one another.
* Android maintains the lower range of user ids (from 1000 to 9999) exclusive for system use.
* The idea behind _capabilities_ is to break to "all-or-nothing" model of the root user: the root user is fully omnipotent, whereas all other users are effectively impotent, so when a user needs to perform some privileged opration, the only standard solution is to become uid 0 for the scope of the operation and then revert to a non-privileged user. Capabilities offer a solution to this problem by "slicing up" the power of root into distinct areas, each represented by a bit in a bitmask, and allowing or restricting privileged operations in these areas only, by toggling the bitmask.
* With this model, even if a given application or user ends up being malicious, its scope of damage is _compartmentalized_.
* SELinux (Security Enhanced Linux) is a set of patches which have long since been incorporated into the mainline kernel, with the aim of providing a Mandatory Access Control (MAC) framework, which can restrict operations to a predefined policy.
* SEAndroid follows the same principle of the original, but extends it to accommodate Android specific features, such as system properties and the Binder. Samsung further extends SEAndroid and uses it as a foundation for their KNOX secure platform.
* The main principle of SELinux is that of _labeling_: a label assigns a type to a resource (object) and a security domain for a process (subject). SELinux can then enforce so as to allow only processes in the same domain to access the resource.
* The stock type enforcement files are all concatenated and compiled into the resulting `/sepolicy` file, which is a binary file placed on the root file system. Doing so offers further security, because the root file system is mounted from the `initramfs`, which is itself part of the `bootimg`, that is digitally signed.
* The `/seapp_contexts` file provides a mapping of applications (as UIDs) to domains - this is used to label processes based on the UID.
* Address Space Layout Randomization (ASLR) attempts to make injection attacks harder by introducing randomness - shuffling the layout of memory regions, making their addresses less predictable. This increases the change a targeted piece of code will be "shifted" in memory, and basically trade a crash in place of compromise by malicious code.
* Working at the level of a virtual machine, rather than native code, brings with it tremendous advantages for monitoring operations and enforcing security. Android actually takes a step further: the user application is entirely powerless, and in order to carry out any operation which has an effect outside the scope of an application, one has to involve `system_server` by calling `getSystemService()`.
* The assignment of permissions to an application is performed when it is loaded and installed - meaning that the user has been notified of the application's requested permissions and has approved them.
* Google requires digital signatures on applications uploaded to the Play Store, so as to identify the developer(s) behind them and add accountability.
* The lock screen is a device's first and only real line of defense against theft or physical interception by malicious entities. The default Android lock screen allows either passwords, PINs or "patterns". It is effectively just an activity, implemented by the `com.android.keyguard` package.
* Alternate lock methods include face recognition, fingerprint scanning, or unlocking using another paired BlueTooth device such as Android Wear by proximity.
* Android hard-codes root certificates in `/system/etc/security/cacerts` and they are encoded in the PEM form, which is a Base64 encoding of the certificate between delimiters. Of special importance are the update certificates stored in `/system/etc/security/otacerts.zip`, which are necessary for applying OTA updates.
* A feature known as **dm-verity** is there for securing the boot process, using the kernel's device mapper. Verifying the integrity of a partition is the simple matter of hashing all of its blocks (DM-Verity uses SHA-256) and comparing that hash against a stored, digitally signed hash value. The dm-verity feature is touted for malware prevention, since it effectively prevents any modification of `/system`, but does have the side effect of preventing unauthorized persistent rooting as well.

## Resources

### Articles

* [The internals of Android APK build process](https://medium.com/androiddevnotes/the-internals-of-android-apk-build-process-article-5b68c385fb20) - Android Dev Notes
* [What are Split APKs?](https://www.emergetools.com/glossary/split-apks) - Emerge Tools

### Books

* [Android Internals: A Confectioners Cookbook](http://newandroidbook.com/) - Jonathan Levin

### Courses

* [Android Basics in Kotlin](https://developer.android.com/courses/android-basics-kotlin/course)

### GitHub Repositories

* [APKLab](https://github.com/APKLab/APKLab) - Android Reverse-Engineering Workbench for VS Code
* [Awesome Android AOSP](https://github.com/Akipe/awesome-android-aosp)
* [Awesome Jetpack Compose Android Apps](https://github.com/androiddevnotes/awesome-jetpack-compose-android-apps)
* [Better `adb shell`](https://github.com/matan-h/adb-shell)
* [Redex](https://github.com/facebook/redex) - A bytecode optimizer for Android apps

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
* [Waydroid](https://waydro.id/) - Android in a Linux container

### YouTube Videos

* [Android Internals for Developers](https://www.youtube.com/watch?v=0zJCyKp7-9s) - Effie Barak, Pinterest
* [Dalvik Virtual Machine Internals](https://www.youtube.com/watch?v=ptjedOZEXPM) - Dan Bornstein, Google
* [Decoding Android Runtime](https://www.youtube.com/watch?v=9e8yQKVkGgo) - Jitin Sharma
