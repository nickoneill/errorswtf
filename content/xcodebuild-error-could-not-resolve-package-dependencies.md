---
title: "Testing with Fastlane and Swift Package Manager on CircleCI / Bitrise: xcodebuild: error: Could not resolve package dependencies"
date: 2020-01-19T17:00:14-08:00
---

## The Problem

If you're running tests on your iOS build CI pipeline with fastlane, you might run into an issue when running `scan` using Xcode 11+ if you've got some Swift package manager dependencies. The full error might look like this:

{{< highlight >}}
[18:44:50]: ------------------
[18:44:50]: --- Step: scan ---
[18:44:50]: ------------------
[18:44:50]: $ xcodebuild -showBuildSettings -workspace FiveCalls/FiveCalls.xcworkspace -scheme FiveCalls
[18:44:53]: Command timed out after 3 seconds on try 1 of 4, trying again with a 6 second timeout...
xcodebuild: error: Could not resolve package dependencies:
  An unknown error occurred. '/Users/vagrant/Library/Developer/Xcode/DerivedData/FiveCalls-gpqeanjdlasujldgqrgmnsakeaup/SourcePackages/repositories/Down-9f901d13' exists and is not an empty directory (-4)
xcodebuild: error: Could not resolve package dependencies:
  An unknown error occurred. could not find repository from '/Users/vagrant/Library/Developer/Xcode/DerivedData/FiveCalls-gpqeanjdlasujldgqrgmnsakeaup/SourcePackages/repositories/Down-9f901d13/' (-3)
{{< / highlight >}}

Coming from this fastfile:

{{< highlight ruby >}}
  desc "Runs all the tests"
  lane :test do
    scan(workspace: "MyProject.xcworkspace",
         scheme: "MySchemeName")
  end
{{< /highlight >}}

The problem here is that Xcode is resolving package dependencies and the build system isn't waiting for that process to complete. Usually this works fine locally, so something is off with the CI timing here.

## The Solution

According to [this issue on the fastlane github](https://github.com/fastlane/fastlane/issues/15454), the problem should be resolved by updating fastlane to 2.138.0+. That didn't fully resolve the issue for me, and there's another way to force updating dependencies before building.

You can force `xcodebuild` to resolve the dependencies in a separate step beforehand, and scan won't run until this completes.

{{< highlight ruby >}}
  desc "Runs all the tests"
  lane :test do
    Dir.chdir("../MyProject") do
      sh("xcodebuild","-resolvePackageDependencies")
    end
    scan(workspace: "MyProject.xcworkspace",
         scheme: "MySchemeName")
  end
{{< /highlight >}}

In this example our `fastfile` is in a `fastlane` directory adjacent to our project directory, so to move from the fastlane directory to our project, we move up one directory and into our project directory (the one with our `xcproject` file). You may need to adjust this for your project setup.
