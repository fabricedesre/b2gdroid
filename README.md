**b2gdroid**

b2gdroid is an experiment that let you use Gaia (the user interface of FirefoxOS) on your Android device, as an alternative homescreen.

**downloads**

The current version is available at https://github.com/fabricedesre/b2gdroid/releases/download/0.11/b2gdroid-41.0a1.en-US.android-arm.apk

**building**

Building b2gdroid is done using:
* The gecko patch from [bug 1170323](https://bugzilla.mozilla.org/show_bug.cgi?id=1170323).
* The [b2gdroid gaia branch](https://github.com/fabricedesre/gaia/tree/b2gdroid).

1. First, create a gaia profile using the b2gdroid distribution directory:

	```
	#!/bin/bash
	export GAIA_DISTRIBUTION_DIR=distros/b2gdroid
	export PRODUCTION=1
	export NOFTU=1
	export MOZILLA_OFFICIAL=1
	rm -rf profile && make profile
	```

2. Create a suitable mozconfig file. The one I'm using is as follows:
	```
	# Build Firefox for Android:
	ac_add_options --enable-application=mobile/android
	ac_add_options --target=arm-linux-androideabi

	# With the following Android SDK and NDK:
	ac_add_options --with-android-sdk="/home/fabrice/.mozbuild/android-sdk-linux/platforms/android-21"
	ac_add_options --with-android-ndk="/home/fabrice/.mozbuild/android-ndk-r8e"

	ac_add_options --enable-android-apz

	# ac_add_options --enable-debug
	ac_add_options --enable-debug-symbols

	ac_add_options --enable-updater

	mk_add_options MOZ_OBJDIR=/home/fabrice/dev/builds/obj-b2gdroid
	mk_add_options AUTOCLOBBER=1
	mk_add_options MOZ_MAKE_FLAGS="-j8 -s"
	```

3. Build b2gdroid. I use a helper script for that, the important part being to set GAIA_DIR properly and to point to the right mozconfig file:
	```
	#!/bin/sh
	export GAIA_DIR=/home/fabrice/dev/gaia/profile
	export MOZCONFIG=/home/fabrice/dev/builds/mozconfig-b2gdroid
	make -C /home/fabrice/dev/gecko -f client.mk && make -C ./obj-b2gdroid/ package
	```

4. Push b2gdroid to your device. This can be done by running `mach install` from your MOZ_OBJDIR directory.
