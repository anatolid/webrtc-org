---
layout: default
title: iOS
permalink: /native-code/ios/
crumb: iOS
---


{% include toc-hide.html %}


### Development Environment

A macOS machine is required for iOS development. While it's possible to
develop purely from the command line with text editors, it's easiest to use
Xcode. Both methods will be illustrated here.


### Getting the Code

  1. Install [prerequisite software][1]

  2. Create a working directory, enter it, and run:

     ~~~~~ bash
     fetch --nohooks webrtc_ios
     gclient sync
     ~~~~~

     This will fetch a regular WebRTC checkout with the iOS-specific parts
     added. The same checkout can be used for both Mac and iOS development,
     since you can generate your [Ninja][4] project files in multiple
     directories (see below).

  3. You may want to disable Spotlight indexing for the checkout to speed up
     file operations..

See [Development][2] for generic instructions on how
to update the code in your checkout.


### Generating project files

[GN][5] is used to generate [Ninja][4] project files. In order to configure
[GN][5] to generate build files for iOS certain variables need to be set.
Those variables can be edited for the various build configurations as needed.

The component build is the default for Debug builds, which are also enabled by
default unless `is_debug=false` is specified. iOS needs static builds, which is
why `is_component_build=false` is specified for all the examples above.


#### Targeting iOS Devices

The different output directories can be replaced with any directory of your own
choice. When running GN; make sure your current working directory is src/ of
your workspace, then run:

~~~~~ bash
gn gen out/Debug-device-arm32 --args='target_os="ios" target_cpu="arm" is_component_build=false'
~~~~~

#### Targeting 64-bit iOS devices

~~~~~ bash
gn gen out/Debug-device-arm64 --args='target_os="ios" target_cpu="arm64" is_component_build=false'
~~~~~

**NOTICE:** To build for devices, you must have a signing identity configured
for your Xcode installation. You can check this by running:

~~~~~ bash
python build/config/ios/find_signing_identity.py
~~~~~

If you need to build for ARM devices without this setup, you can add the
`ios_enable_code_signing=false` variable to GN's arguments.

#### Targeting the iOS Simulator

~~~~~ bash
gn gen out/Debug-sim32 --args='target_os="ios" target_cpu="x86" is_component_build=false'
~~~~~

#### Targeting 64-bit iOS Simulator

~~~~~ bash
gn gen out/Debug-sim64 --args='target_os="ios" target_cpu="x64" is_component_build=false'
~~~~~

#### Targeting macOS

The host OS is the default `target_os`, so you don't need to specify it:

~~~~~ bash
gn gen out/Debug-mac
~~~~~

### Compiling with ninja

To compile, just run ninja on the appropriate target. For example:

~~~~~ bash
ninja -C out/Debug-device-arm32 AppRTCMobile
~~~~~

Some sample scripts are also available in [webrtc/build/ios][3].


### Compiling with Xcode

Compiling with Xcode is not supported! What we do instead is compile using a
script that runs ninja from Xcode. In order to generate the relevant Xcode
project, add `--ide=xcode` to the GN command. By using Xcode in this manner, we
get the build speed of ninja while at the same time getting access to the usual
methods of deployment/debugging for iOS.

After running GN, you'll find a `all.xcworkspace` file in the output directory.
Using this, you can select the desired target and platform in the Xcode usual
fashion and build/deploy. Note that you will need to rerun GN if you want to
switch target platforms.


### Deploying to Device

It's easiest to deploy to a device using Xcode. Other command line tools exist
as well, e.g. `ios-deploy`.


[1]: {{ site.baseurl }}/native-code/development/prerequisite-sw/
[2]: {{ site.baseurl }}/native-code/development/
[3]: https://chromium.googlesource.com/external/webrtc/+/master/webrtc/build/ios
[4]: https://chromium.googlesource.com/chromium/src/+/master/docs/ninja_build.md
[5]: https://chromium.googlesource.com/chromium/src/+/master/tools/gn/README.md
