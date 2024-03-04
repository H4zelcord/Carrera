## IOS

#### Kernel Architecture Overview.-

OS X provides some benefits to the Macintosh user and developer communities. These being improved reliability and performance, enhanced networking, Object-based system programming interface and increased support for industry standards.
By creating OS X, Apple re-engineered Mac OS core OS.
The kernel provides many enhancements for OS X, like preemption, memory protection, and the ones listed before. It also supports both Macintosh and non-Macintosh file systems, OO APIs and more.
OS X is a preemptive multitasking environment, its kernel provides enforcement of cooperation and scheduling processes to share time. Supporting real-time behavior in applications that require it.
Processes usually don't share memory in OS X, instead, the kernel assigns each process its own address space, controlling said access to ensure no application access or modify another app's memory. Also, size isn't a problem thanks to the virtual memory system in OS X.

The OS X kernel is an open source project, being referred to as "Darwin", that OS is based on many of the same technologies that underlie OS X, but not including Apple's proprietary graphics or application layers, like Quartz, QuickTime, Cocoa, Carbon or OpenGL.

OS X and Darwin share a common core, allowing developers to create low-level software for both systems with minimal adjustments. Darwin integrates proven technologies from sources like FreeBSD and Mach, offering platform-independent code available in source form.
Mach provides clean abstractions for critical operating-system functions, while BSD contributes mature engineering and industry-standard APIs. These technologies create a stable layer between the operating system and hardware, crucial for adaptability in a changing hardware environment.
OS X introduces innovations like the I/O Kit and Network Kernel Extensions (NKEs), designed by Apple to leverage advanced capabilities through an object-oriented programming model. Combined with established industry standards, this integration results in a stable, reliable, flexible, and extensible operating system. The thoughtful blend of proven and new technologies underscores the strategic development of OS X (macOS) on the Darwin foundation.

###### Architecture.-

- Mach
Mach manages processor resources such as CPU usage and memory, handles scheduling, memory protection and messaging-centered infrastructure to the rest of the OS layers. The mach component provides:
-IPC (Untyped interprocess comunication)
-RPC (Remote procedure calls)
-SMP (Scheduler support for symmetric multiprocessing)
-Support for real-time services
-Virtual memory support
-Support for "pagers"
-Modular architecture

- BSD
The BSD layer provides "OS personality" APIs and services. It provides:
-File systems
-Networking(Not on the hardware device level)
-UNIX security model
-`syscall`support
-The BSD process model, with process IDs and signals
-FreeBSD kernel and APIs
-Many of the POSIX APIs
-Kernel support for pthreads

- Networking
OS X's networking takes advantage of BSD's advanced networking capabilities to provide support for modern features, like network Address Translation and firewalls. The networking component provides:
-4.4BSD TCP/IP stack and socket APIs
-support for both IP and DDP (AppleTalk transport)
-multihoming
-routing
-multicast support
-server tuning
-packet filtering
-Mac OS Classic support (through filters)

- File Systems
OS X supports File Systems like HFS/+, UFS, NFS, ISO 9660... The default one is HFS+, advanced features of OS X file systems include an enhanced Virtual File System design, which provides layered architecture. the FS provides:
-UTF-8 (Unicode) support
-Increased performance over previous Mac OS versions.

- I/O Kit
Provides a framework for simplified driver development, supporting many categories of devices. The I/O Kit features an OO I/O architecture implemented in a restricted subset of C++. The framework is both modular and extensible. The I/O Kit provides:
-True plug and play
-Dynamic device management
-Dynamic loading of drivers ("On demand")
-Power management for Desktop Systems as well as portables
-Multiprocessor capabilities.

###### Kernel Extensions

OS X provides a kernel extension mechanism known as plug-ins or KEXTs, these provide modularity and dynamic loadability, and are a natural choice for any self-contained services requiring access to interfaces not exported to user space, many of the components of the kernel environment support this extension mechanism.


#### The Early Boot Process.-

###### The Boot Loader

Once BootROM is finished and an OS X partition has been selected, control passes to the boot.efi boot loader, which loads the kernel environment and draws the "booting" image on screen. If full-disk encryption is enabled, the boot loader is responsible for drawing the login UI and prompting for the user's password, which needed to access the encrypted disk to boot from it.

In order to speed up boot time, the boot loader uses several caches, whose contents and location vary between versions of OS X.
If the caches cannot be used, the boot loader searches `/System/Library/Extensions`for drivers, and other kernel extensions whose `OSBundleRequired` property is set to a value appropiate to the type of boot. This is a really slow process, as the `Info.plist` file of every kernel extension must be parsed and the linker must be run.

###### Rooting

Once the kernel and all drivers necessary for booting are loaded, the boot loader starts the kernel's initialization procedure. At this point, enough drivers are loaded for the kernel to find the root device. The kernel then initializes the Mach and BSD data structures, and the I/O Kit, which then links the loaded drivers into the kernel, using the device tree to determine which ones to link. Once the kernel finds the root device, it roots BSD off of it.


#### Mach Scheduling and Thread Interfaces.-

###### Overview of Scheduling

Threads can migrate between priority levels for a number of reasons, largely as an artifact of the time sharing algorithm used. However, this migration is within a give band.
###### Why did my Thread Priority Change?

Real-time threads face potential demotion to normal priority if they exceed their time quantum without blocking, emphasizing the importance of accurately estimating workload.
Compute-bound threads receive lower priority to minimize response time for interactive tasks, preventing the monopolization of the system by high-priority compute-bound threads. Even at a reduced priority, compute-bound threads run frequently, facilitated by higher-priority I/O-bound threads that perform brief processing and then allow compute-bound threads to execute.
The Mach scheduler continually adjusts thread priorities based on behavior, ensuring responsiveness and efficient resource utilization.

###### Using Mach Scheduling From User Applications

- Using the `pthreads` API to influence Scheduling
The `pthreads` API adjusts the priority of threads in other task. To increase the priority of a task you can use `nice` or `renice` from the command line, or call `getpriority` and `setpriority` from your application.
The API provides `pthread_getschedparam(pthread_t thread, int policy, struct sched_param *param)` and `pthread_setschedparam(pthread_t thread, int policy, struct sched_param *param)`.
They get as arguments, a thread ID, pointers to memory where results are/will be stored, the policy... (FIFO, RR, Other...).

- Using the Mach Thread API to influence Scheduling
This API is often used in multimedia applications to obtain real-time priority.
Uses `thread_policy_set` and `thread_policy_get`. Both get almost the same parameters, but the get function takes pointers for the count, and get_default arguments. The count is an inout parameter.

- Using the Mach Task API to influence Scheduling
This API is not particularly useful for most developers. However, it may be beneficial if you are developing a graphical user interface for Darwin. It also provides insight into the prioritization of tasks in OS X. The API consist of `task_policy_set(task_t task, task_policy_flavor_t flavor, task_policy_t policy_info, mach_msg_type_number_t count)` and `task_policy_get(task_t task, task_policy_flavor_t flavor, task_policy_t policy_info, mach_msg_type_number_t *count, boolean_t *get_default)`. Their parameters are similar, except that the get takes pointers for the count and the get_default arguments. The count argument is an inout parameter. The functions get and set a parameter that functions as a role of a given task, which changes the way the task's priority gets altered over time.

###### Kernel Thread APIs

- Creating and Destroying Kernel Threads
The recommended interface for creating threads within the kernel is through the I/O Kit. It provides `IOCreateThread`, `IOThreadSelf` and `IOExitThread`.
With the exception of `IOCreateThread`, the I/O Kit functions are fairly thin and easy to understand. `IOThread` is pretty much the same as `thread_t`.
The `IOCreateThread` function creates a new thread that begins executing the function specified, passing a single argument to that function.

###### SPL and Friends

BSD-based and Mach-based OS contain legacy functions designed for basic single-processor synchronization. Including some like `splhigh`, `splbio`, `splx` and more. Since these legacy functions are not useful for synchronization in an SMP situation, they aren't as well in OS X.

###### Wait Queues and Wait Primitives

The functions related to wait queues and thread synchronization cover various aspects of event handling and concurrency control.

Most functions take an `event_t` as an argument, often represented by the address of a data object related to the code. Functions concluding with `_locked` demand a lock on the wait queue before utilization.

Functions like `wait_queue_alloc` and `wait_queue_init` introduce a policy parameter, offering choices like `SYNC_POLICY_FIFO` and `SYNC_POLICY_FIXED_PRIORITY`.

Exercise caution when using `wait_queue_init` outside the scheduler. Instead, opt for `wait_queue_alloc` and `wait_queue_free` unless specifically working within the scheduler context.

Several functions, including `wait_queue_member` and `wait_queue_link`, operate exclusively on subordinate queues, not exposed beyond the scheduler.

The `wait_queue_assert_wait` function prompts a thread to wait on a queue until interrupted or explicitly awakened by another thread, with the `interruptible` flag allowing asynchronous events to interrupt waiting.

Functions like `wait_queue_wakeup_all` and `wait_queue_wakeup_one` play a crucial role in waking up threads waiting on a queue for a specific event.

The `wait_queue_peek_locked` function returns the first waiting thread from a queue without removing or waking it. It also provides information about the wait queue where the thread was found.

For selective thread manipulation, `wait_queue_pull_thread_locked` extracts a thread from a wait queue, optionally unlocking it. Typically, it is used with the result of a previous call to `wait_queue_peek_locked`. The `wait_queue_wakeup_identity_locked` function wakes up the first thread waiting for an event, leaving it locked for subsequent modifications.

The `wait_queue_wakeup_thread` function awakens a specific thread only if it is waiting on a specified event and wait queue.

Finally, the `wait_queue_remove` function provides a means to wake a thread without regard to the wait queue or event on which it is waiting. This suite of functions caters to nuanced aspects of thread synchronization and event handling within wait queues.

---

#### Managing your app's lifecycle.-

###### Overview

The state of your app determines what it can and can't be done at any time, as an example, a foreground app has the user's attention, so it needs priority over system resources, like the CPU. In contrast, a background app shouldn't do any work if possible, as it's offscreen. The behavior must be adjusted accordingly.
UI Kit notifies you by calling methods when your app's state changes.

###### Respond to scene-based life-cycle events

If the app supports scenes, UI Kit delivers separate life-cycle events for each one. A scene represents one instance of the app's UI running on a device. Multiples scenes can be created, and can be hid and shown separately. Each scene can be in a different state of execution, as they have their own life cycle.

When the user or system requests a new scene for the app, UI Kit creates it and puts it in the unattached state. User-requested scenes move quickly to the foreground, where they appear onscreen. A system-requested scene typically moves to the background so that it can process an event.
UI Kit can disconnect a background or suspended scene at any time to reclaim its resources, returning that scene to the unattached state.

Scene transitions can be used to do the following:
-Configure the scene's initial UI and load the data the scene needs.
-Configure the UI and prepare it to interact with the user.
-Upon leaving the foreground-active state, save data and quiet the app's behavior.
-Upon entering said state, finish crucial tasks, free up memory and prepare the app snapshot.
-At scene disconnection, clean up shared resources associated with it.
-Respond to the launch of your app using the `UIApplicationDelegate` object.

###### Respond to app-based life-cycle events

In apps that don't support scenes, UI Kit delivers all life-cycle events to the `UIApplicationDelegate` object. The app then delegate manages all of the windows including those on separate screens, making it that state transitions affect the entire UI, even on external displays.

App transitions can be used to do the following tasks:
-Initialize the app's data structures and UI on launch
-Finish configuring the UI and prepare it to interact with the user
-Save data and quiet the app's behavior upon deactivation
-Finish crucial tasks, freeing up memory and preparing the app snapshot upon entering background state
-Stop all work immediately and release shared resources on termination

###### Respond to other significant events

Apps must be prepared to handle the following events:
-Memory warnings
-Protected data becoming available/unavailable
-Time changes
-URL opening

This can be done using the `UIApplicationDelegate` object, in certain cases it's possible to handle these using notifications, allowing response from different parts of the app.


---

## Android
#### Android Architecture.-

Android is an open source, Linux-based software stack created for a wide array of devices and form factors. Its formed by the Linux Kernel, the Hardware Abstraction Layer, Android Runtime, Native C/C++ libraries, Java API Framework and System Apps.

###### Linux Kernel

It's the foundation of the Android platform, like the ART, which relies on the Linux Kernel for underlying functionalities like threading and low-level memory management.
Using the Linux Kernel lets Android take advantage of key security features and let's manufacturers develop hardware drivers for a well-known kernel.

###### Hardware Abstraction Layer

It provides standart interfaces that expose device hardware capabilities to the higher-level Java API Framework. The HAL consist of multiple library modules, each of which implements an interface for a specific type of hardware component, like the camera or Bluetooth module. When a framework API makes a call to access device hardware, the Android system loads the library module for that hardware component.

###### Android Runtime

For API level 21 or higher devices each app runs in its own process and with its own instance of the Android Runtime. ART is written to run multiple virtual machines on low-memory devices by executing DEX files, which is a bytecode format designed specifically for Android that is optimized for a minimal memory footprint. Build tools like `d8` compile Java sources into DEX bytecode which can run on the Android platform.

The following are some of the mayor features of ART:
-Ahead-of-time and just-in-time compilation
-Optimized garbage collection
-Conversion of an app package's DEX files to more compact machine code
-Better debugging support, dedicated sampling profiler, detailed diagnostic exceptions and crash reporting.

###### Native C/C++ libraries

Many core Android system components and services such as ART and HALT are built from native code that requires native libraries written in C and C++. The Android platform provides Java framework APIs to expose its functionality to apps.

###### Java API Framework

The entire feature set of the Android OS is available through APIs written in Java. These APIs form the building blocks you need to create Android apps by simplifying the reuse of core, modular system components and services, which include the following:
-A rich and extensible view system available to build apps UI, including lists, grids, text boxes, buttons and embeddable web browser.
-A resource manager
-A notification manager
-An activity manager
-Content providers
###### System Apps

Android comes with a set of core apps for email, SMS, calendars, internet browsing, contacts and more. These apps are no special than others, just set by default, meaning the user can change them by installing others, with some exceptions like the system's Settings app. Any system app function as an user app, but also provides key capabilities that developers can access from their own app.

---

## COMPARISON BETWEEN BOTH

There are so many topics to compare iOS and Android with, but I've selected the most relevant ones, that I haven't highlighted before:
###### Worldwide smartphone OS in market:

The text highlights some data off 2015, given by stadista that shows how Android dominates the numbers, having over 81.6% of the smartphones with its OS, meanwhile iOs holds the 15.9%. These two represent almost the entire unit sales on OS.

###### Price Gap

From data provided by stadista, we can see how the price gap between iOS and Android keeps on increasing, with it being around 400$ of difference in 2014.

###### Revenue

We can also note that, despite the price gap, and the fact that most smartphones are Android OS, another stadista study shows both Samsung's and Apple's revenue over the years, capping at 2016, where the difference was around 70 billion dollars in revenue, in Apple's favor.