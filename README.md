**Introduction:**

I wasn't able to play Genshin Impact because I game with inverted-y axis mouse.  After looking everywhere, the only functional one was [MAF-soft mouse driver](https://www.maf-soft.de/mafmouse/).  This was close, but did not have on-the-fly toggling for x64 builds, which was crucial for games like Genshin Impact.

I decided to adapt the official [Firefly Microsoft USB mouse filter sample project](https://github.com/microsoft/Windows-driver-samples/tree/master/hid/firefly) in the [WDK](https://docs.microsoft.com/en-us/windows-hardware/drivers/download-the-wdk).  This software needs to be a Windows Kernel Driver because it's the only way to access mouse telemetry for games.  I'd expect miHoYo to release this very simple fix to their excellent game very soon, but in case you can't wait, I'm playing it very well now with this driver installed.

Update 2/6/21: Code changed to match X & Y axis sensitivities as follows. Currently, miHoYo apparently has about 3x differental between X & Y axis sensitivities.  Compensate by multiplying Y axis by 3, but only in inverted mode.  If you don't need this compensation, use the original pre-built binary or modify driver.c and change YMultiplier = 1.

**What It Is:**

A mouse filter driver for Windows x64 that allows on-the-fly toggling of Y axis.

**How It Works:**

This  is a Kernel Filter Driver for the the HID mouse driver that changes mouse telemetry at a low level.
Once installed as a driver to your USB mouse, the mouse using this driver works normally until:

* Simultaneous Right-Left-Drag Down, then release: Inverted Y
* Simultaneous Right-Left-Drag Up, then release: Normal Y

On the fly switching is required because the game has many menu interactions which required normal and not inverted Y. The scheme of two button simultaneous drag was chosen to be easy to trigger but hard to accidentally do so.

Note, the mouse driver works for Windows universally, and does not know about any games.  So you can test out inversion toggling on the desktop, for example, or use it with any other games.

**Warnings:**

Now here's the bad news.  For understandable security reasons, Windows 10 will not allow unsigned kernel drivers to be installed or run, unless computer is in testing mode.  I'm not a Windows software developer, and do not have certificate to sign this simple driver, and don't want to spend $100+ to get one for this temporary fix (until miHoYo inevitably releases a fix soon).  So if you can't wait, like me, you WILL need to put your computer into Test Mode to allow Windows to load unsigned driver!

WARNING: While your computer is in Test Mode (and it must be for this unsigned driver to work), Windows will not be enforcing trusted drivers for you!  You alone will be responsible for deciding which driver you trust.

**How To Build:**

How to build this driver is beyond the scope of this post, and I did much paring of the code to use only what's needed.  The complete source for building this "Invertible USB Mouse Filter Driver" is available as a fork of the official Microsoft WDK sample named [Invertible USB Mouse Filter Driver](https://github.com/tthk/Invertible-USB-Mouse-Driver-Filter-Driver).  If you want to give it a try building, I used
[this resource](https://docs.microsoft.com/en-us/windows-hardware/drivers/develop/building-a-driver)
.  Pre-build unsigned x64 binaries
([Y sensitivity as is](https://github.com/tthk/Windows-driver-samples/raw/master/Invertible%20USB%20Mouse%20Filter.zip)
and
[Y sensitivity x3](https://github.com/tthk/Windows-driver-samples/raw/master/Invertible%20USB%20Mouse%20Filter%203Y.zip))
are available for download in the Github repo.

**How To Install:**

1. [Prepare your computer to run unsigned drivers](https://www.maketecheasier.com/install-unsigned-drivers-windows10/).  I find putting Windows in Test Mode is easiest option.
2. Either
[build](https://docs.microsoft.com/en-us/windows-hardware/drivers/develop/building-a-driver)
or download driver from repository and unzip:
	* [Windows x64 build (Y sensitivity as is)](https://github.com/tthk/Windows-driver-samples/raw/master/Invertible%20USB%20Mouse%20Filter.zip)
	* [Windows x64 build 3Y (Y sensitivity x3)](https://github.com/tthk/Windows-driver-samples/raw/master/Invertible%20USB%20Mouse%20Filter%203Y.zip)
3. Open "Computer Management" -> "Device Manager"
4. Find your mouse under "Mice and other pointing devices", right-click, "Update driver"
5. "Browse my computer for drivers" -> "Let me pick from a list of available drivers on my computer" -> "Have Disk..."
6. "Browse" then Navigate to your unzipped driver directory until you can double-click on "firefly.inf" -> "OK"
7. Select "u/GuppyLive Invertible USB Mouse Filter" -> "Next"
8. Accept all the dire warnings about unsigned drivers, and you're done!


TLDR:

Working solution for on-the-fly inversion of mouse Y axis,  to play games like Genshin Impact before it gets this feature built-in.  However, getting this to work requires putting your computer in Test Mode because driver built is unsigned.

And Genshin Impact miHoYo, please make this driver obsolete for your game soon!

Have fun!  
