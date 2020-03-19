---
title: "openjdk cannot be opened because the developer cannot be verified when installing adb via brew"
date: 2020-03-19T8:00:14-08:00
---

## The Problem
![openjdk cannot be opened because the developer cannot be verified when installing adb via brew](/images/openjdk-cannot-be-opened-because-the-developer-cannot-be-verified.png)

If you're like me and enjoy the simplicity of installing command line tools using the `brew` command on macOS, you've likely run into one or two cases where Catalina prevents you from running a tool that's been installed because it hasn't been verified.

In this case, I'm installing the android developer tools for React Native development and needed both `adb` and `openjdk`. I've used both of these commands to install them:

`brew cask install android-platform-tools`
`brew cask install java`

This situation is similar to downloading a new Mac app from any developer online. Some developers want to distribute apps without the restrictions placed on them by Apple and can run unsigned code - with some restrictions.

## The Solution
The issue is that macOS labels all downloaded binaries with a "quarantine" attribute which tells the system that it should not be run automatically before being explicitly approved by the user.

If you're installing an app, the sneaky way to allow opening unsigned code is to use Right Click -> Open rather than double clicking on the app icon itself. That'll allow you to approve removing the quarantine and you can open with a double click next time.

This even works in some cases with command line tools: you can use the `open some/path/to/a/folder` from Terminal to open a folder in the finder that contains `adb` and then right click it to get the standard bypass quarantine prompt.

The JDK is more tricky since it's a folder and not an application. You can't just right click to launch it, instead you have to manually remove the quarantine attributes from the folder where it's been downloaded. You can do this easily in the terminal with this command:

`xattr -d com.apple.quarantine /Library/Java/JavaVirtualMachines/adoptopenjdk-13.0.1.jdk`

The command line tool `xattr` is used for modifying or inspecting file attributes on macOS. The `-d` flag removes an attribute, `com.apple.quarantine` is the quarantine attribute for unsigned code we discussed earlier and the final argument is the path to the file. Your jdk might have a different version or a different tool might be in an entirely different location.

---

As usual, quarantine is there to protect your computer from unsigned software. Please ensure you trust the developer you're running unsigned code from before opening it on your machine.
