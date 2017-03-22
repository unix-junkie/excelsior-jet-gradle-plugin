[![Maven Central](https://img.shields.io/maven-central/v/com.excelsiorjet/excelsior-jet-gradle-plugin.svg)](https://maven-badges.herokuapp.com/maven-central/com.excelsiorjet/excelsior-jet-gradle-plugin)
Excelsior JET Gradle Plugin
=====

*Excelsior JET Gradle Plugin* provides Gradle users with an easy way to compile their applications
down to optimized native Windows, OS X, or Linux executables with [Excelsior JET](http://excelsiorjet.com).
Such precompiled applications start and often work faster, do not depend on the JRE,
and are as difficult to reverse engineer as if they were written in C++.

  * [Basic Usage](#basic-usage)
  * [Full Documentation](#full-documentation)
  * [Sample Project](#sample-project)
  * [Communication](#communication)
  * [Release Notes](#release-notes)
  * [Roadmap](#roadmap)


## Basic Usage

**Notice:** The Excelsior JET Gradle plugin requires the Java plugin
be applied beforehand: `apply plugin: 'java'`

The current version of the plugin supports four types of applications:

*   _Plain Java SE applications_, defined as applications that (a) can be run
    with all dependencies explicitly listed on the command-line
    of the conventional `java` launcher:
    `java [-cp` _dependencies-list_ `] `_main-class_
    and (b) load classes mostly from the listed jars,

*   [Tomcat Web applications](https://www.excelsiorjet.com/solutions/protect-java-web-applications)
    — `.war` files that can be deployed to the Apache Tomcat application server,

*   **Invocation dynamic libraries** (e.g. Windows DLLs) callable from non-JVM languages, and

*   Java applications disguised as **Windows services** using the
    [Excelsior JET WinService API](https://www.excelsiorjet.com/docs/WinService/javadoc/)

Assuming that a copy of Excelsior JET is accessible via the operating system `PATH`,
here is what you need to do to use it in your Gradle project:

1. Unless you want to create a Windows Service, skip to Step 4.

2.  Add a dependency on the Excelsior JET WinService API to your Gradle project,
    e.g. by copying and pasting the following snippet to the `dependencies{}`
    section of your `build.gradle` file:

    <pre>dependencies {
        compileOnly "com.excelsiorjet:excelsior-jet-winservice-api:1.0.0"
    }</pre>

3.  Implement a subclass of `com.excelsior.service.WinService` as described in the
    [Excelsior JET WinService API documentation](https://www.excelsiorjet.com/docs/WinService/javadoc/).


4.  Add the plugin dependency in the `buildscript{}` configuration of the `build.gradle` file
    and apply the `excelsiorJet` plugin:

        buildscript {
            ext.jetPluginVersion = '0.9.5'
            repositories {
                mavenCentral()
            }
            dependencies {
                classpath "com.excelsiorjet:excelsior-jet-gradle-plugin:$jetPluginVersion"
            }
        }

        apply plugin: 'excelsiorJet'

5.  Depending on the type of your application,
    configure the `excelsiorJet{}` section as follows:

    **Plain Java SE Application:**


        excelsiorJet {
            mainClass = ''
        }

    Set the value of the `mainClass` parameter to the
    name of the main class of your application.

    **Tomcat Web Application:**


        excelsiorJet {
            tomcat {
                tomcatHome = ""
            }
        }

    Set the `tomcatHome` parameter to point to the
    _master_ Tomcat installation — basically, a clean Tomcat instance that was never launched, and

    **Invocation Library:**


        excelsiorJet {
            appType = "dynamic-library"
        }

    **Warning:** Testing and using dynamic libraries that expose Java APIs is tricky.
    Make sure to read the respective
    [section](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Invocation-Dynamic-Libraries)
    of the plugin documentation.

    **Windows Service: **


        excelsiorJet {
            appType = "windows-service"
            mainClass = "service-main" // <--- Your WinService implementation
            windowsService{
                name = ""
                displayName = ""
                description = ""
                arguments  = []
                logOnType = ""
                allowDesktopInteraction = false
                startupType = ""
                startServiceAfterInstall = true
                dependencies = []
            }
        }


    where `service-main` is the name of the class that you implemented on Step 3.
    For descriptions of all other parameters, refer to
    [plugin documentation](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Windows-Services).

    For more information about Windows services support in Excelsior JET,
    refer to to the "Windows Services" Chapter of the
    [Excelsior JET for Windows User's Guide.](https://www.excelsiorjet.com/docs/jet/jetw)


6.  Use the following command line to build the application:

        gradlew jetBuild

Refer to [plugin documentation](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki) for further instructions.


## Full Documentation

See the [Wiki](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki) for full documentation on the plugin.

  * [Home](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki)
  * [Prerequisites](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Prerequisites)
  * [Getting Started](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Getting-Started)

**Compilation Settings:**

  * [Dependency-Specific Settings](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Dependency-Specific-Settings)
  * [Optimizations](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Optimization-Settings)
  * [Target Executable](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Target-Executable-Settings)
  * [Application Apperarance](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Application-Appearance)

**Packaging Settings:**

  * [Package Contents](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Customizing-Package-Contents)
  * [System Properties And JVM Arguments](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Defining-System-Properties-And-JVM-Arguments)
  * [Excelsior JET Runtime](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Excelsior-JET-Runtime-Configurations)
  * [Excelsior Installer (Windows/Linux)](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Excelsior-Installer-Configurations)
  * [OS X App Bundles And Installers](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Creating-OS-X-Application-Bundles-And-Installers)

**Application Type Specifics:**

  * [Plain Java SE Applications](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Plain-Java-SE-Applications)
  * [Tomcat Web Applications](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Tomcat-Web-Applications)
  * [Dynamic Libraries](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Invocation-Dynamic-Libraries)
  * [Windows Services](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/wiki/Windows-Services)


Refer to the [Excelsior JET User's Guide](https://www.excelsiorjet.com/docs)
and [Knowledge Base](https://www.excelsiorjet.com/kb)
for complete usage information.


## Sample Project

To demonstrate the process and result of plugin usage, we have forked the [Pax Britannica](https://github.com/libgdx/libgdx-demo-pax-britannica) Libgdx demo project on GitHub,
added the Excelsior JET plugin to its `build.gradle` file, and run it through Gradle to build native binaries for three platforms.

You can download the binaries from here:

* [Windows (32-bit, 27MB installer)](http://www.excelsior-usa.com/download/jet/gradle/pax-britannica-windows-x86.exe)
* [OS X (64-bit, 50MB installer)](http://www.excelsior-usa.com/download/jet/gradle/pax-britannica-osx-amd64.pkg)
* [Linux (64-bit, 37MB installer)](http://www.excelsior-usa.com/download/jet/gradle/pax-britannica-linux-amd64.bin)

or clone [the project](https://github.com/excelsior-oss/libgdx-demo-pax-britannica) and build it yourself:

```
    git clone https://github.com/excelsior-oss/libgdx-demo-pax-britannica
    cd libgdx-demo-pax-britannica
    gradlew :desktop:jetBuild
```


## Communication

To report a bug in the plugin, or suggest an improvement, use
[GitHub Issues](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/issues).

To receive alerts on plugin and Excelsior JET updates, subscribe to
the [Excelsior JET RSS feed](https://www.excelsior-usa.com/blog/category/excelsior-jet/feed/),
or follow [@ExcelsiorJET](https://twitter.com/ExcelsiorJET) on Twitter.


## Release Notes

Version 0.9.5 aka 1.0 Release Candidate (15-Feb-2017)

This release covers all Excelsior JET features accessible through the JET Control Panel GUI,
and all options of the `xpack` utility as of Excelsior JET 11.3 release, except for three things
that we do not plan to implement in the near future, for different reasons:
creation of update packages, Eclipse RCP applications support, and internationalization
of Excelsior Installer messages.
If you are using any other Excelsior JET functionality that the plugin does not support,
please create a feature request [here](https://github.com/excelsior-oss/excelsior-jet-gradle-plugin/issues).
Otherwise, think of this version as of 1.0 Release Candidate 1.

Compared with the previous releases, the following functionality was added to the plugin:

* `packageFiles` parameter introduced to add separate files/folders to the package
* `excelsiorInstaller{}` configuration section extended with the following parameters:
    - `language` - to set installation wizard language
    - `cleanupAfterUninstall` - to remove all files on uninstall
    - `afterInstallRunnable` - to run an executable after installation
    - `compressionLevel` - to control installation package compression
    - `installationDirectory` - to change installation directory defaults
    - `registryKey` - to customize the registry key used for installation on Windows
    - `shortcuts` - to add shortcuts to the Windows Start menu, desktop, etc.
    - `noDefaultPostInstallActions` - to not add the default post-install actions
    - `postInstallCheckboxes` - to configure post-install actions
    - `fileAssociations` - to create file associations
    - `installCallback` - to set install callback dynamic library
    - `uninstallCallback` - to set uninstall callback dynamic library
    - `welcomeImage`, `installerImage`, `uninstallerImage` - to customize (un)installer appearance
* `allowUserToChangeTomcatPort` parameter added to the `tomcat{}` configuration section
  to allow the user to change the Tomcat port at install time

Version 0.9.4 (24-Jan-2017)

* `typical` and `smart` optimization presets introduced.

Version 0.9.3 (19-Jan-2017)

* `runtime{}` configuration section introduced and related parameters moved to it:
   `locales`, `profile`, `optRtFiles` (renamed to `components`), `javaRuntimeSlimDown` (renamed to `slimDown`).
   Old configuration parameters are now deprecated and will be removed in a future release.
   New parameters added to the `runtime{}` section:
    - `flavor` to select a runtime flavor
    - `location` to change runtime location in the resulting package
    - `diskFootprintReduction` to reduce application disk footprint

* Windows version-info resource configuration changed to meet other enclosed configurations style.
  Old way to configure Windows version info is deprecated and will be removed in a future release.


Version 0.9.1 (02-Dec-2016)

* Support for Compact Profiles
* Not working Test Run for 7+ Tomcat versions fixed

Version 0.9.0 (23-Nov-2016)

Invocation dynamic libraries and Windows services support.

Version 0.8.1 (28-Oct-2016)

The release supports [Excelsior JET Embedded 11.3 for Linux/ARM](https://www.excelsiorjet.com/embedded/).

Version 0.8.0 (20-Oct-2016)

The release adds the capability to set Excelsior JET-specific properties for project dependencies,
such as code protection, selective optimization, and resource packing.

Version 0.7.2 (19-Aug-2016)

This release adds the capability to pass command-line arguments to the application
during startup profiling and the test run.

Version 0.7.1 (10-Aug-2016)

This release covers most of the compiler options that are available in the JET Control Panel UI,
and all options of the `xpack` utility as of Excelsior JET 11.0 release:

  * `splash` parameter introduced to control the appearance of your application on startup
  * `inlineExpansion` parameter introduced to control aggressiveness of methods inlining
  * `stackTraceSupport` parameter introduced to set stack trace support level
  * `compilerOptions` parameter introduced to set advanced compiler options and equations
  * `locales` parameter introduced to add additional locales and charsets to the resulting package

Version 0.7.0 (12-Jul-2016)

* Compilation of Tomcat Web applications is supported

Version 0.3.0 (06-Jul-2016)

* Support of Excelsior Installer setup generation
* Windows Version Information generation
* Support of multi-app executables
* Startup Accelerator supported and enabled by default
* Test Run Task implemented that enables:
   - running an application on the Excelsior JET JVM before pre-compiling it to native code
   - gathering application execution profiles to enable the Startup Optimizer
* `optRtFiles` parameter introduced to add optional JET runtime components
* Reduced the download size and disk footprint of resulting packages by means of supporting:
   * Global Optimizer
   * Java Runtime Slim-Down
* `packageFilesDir` parameter introduced to add extra files to the final package
* Trial version generation is supported
* `jvmArgs` parameter introduced to define system properties and JVM arguments
* `protectData` parameter added to enable data protection
* Mac OS X application bundles and installers support

Version 0.1.0 (24-Jun-2016)
* Initial release supporting compilation of the Gradle Project with all dependencies into native executable
and placing it into a separate directory with required Excelsior JET runtime files.

## Roadmap

Even though we are going to base the plugin development on your feedback in the future, we have our own short-term plan as well.
So the next few releases will add the following features:

* Excelsior JET 11.3 release features support
* Multi-component support: building dependencies into separate native libraries
                           to reuse them across multiple Gradle project builds
                           so as to reduce overall compilation time
* Code signing.

Note that the order of appearance of these features is not fixed and can be adjusted based on your feedback.
