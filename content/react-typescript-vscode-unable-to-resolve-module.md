---
title: "React Native, Typescript and VS Code: Unable to resolve module"
description: "The problem with resolving modules turned out to be related to VS Code's typescript project helper."
date: 2020-02-11T10:00:14-08:00
---

## The Problem

I've run into this problem largely when setting up new projects, as I start to break out internal files into their own folders and the project has to start finding dependencies in new locations.

In my case, it was complaining about imports from internal paths like `import ContactPermissions from 'app/components/screens/contactPermissions';`.

The error message tries to help by giving you four methods for resolving the issue, which seem to work only in the most naive cases:

### Reset the tool that watches files for changes on disk:
`watchman watch-del-all`

### Rebuild the node_modules folder to make sure something wasn't accidentally deleted
`rf -rf node_modules && yarn install`

### Reset the yarn cache when starting the bundler
`yarn start --reset-cache`

### Remove any temporary items from the metro bundler's cache
`rm -rf /tmp/metro-*`

These cases might work for you if your problem is related to external dependencies that may have changed (maybe you changed your `node_modules` without re-running `yarn` or installed new packages without restarting the packager).

In the case with VS Code, this *did not* resolve my issues. I was still running into issues where modules could not be found.

## The Solution

The problem here turned out to be related to VS Code's typescript project helper. When I referenced existing types in my files, VS Code was automatically importing the file for me - this is usually very helpful!

But for whatever reason, the way my app is set up means that even though VS Code could tell where `app/components/screens/*` was located (an incorrect import path usually causes VS Code to report an error on that line), typescript had trouble determining where this file lived from this path. Even being more specific about the start of the path with `./app/components/...` was not working for the typescript plugin.

What *did* work was using relative paths in my typescript files. So instead of referencing files from `app/components/screens/contactPermssions`, I would use `../components/screens/contactPermissions` for a file that was located in a different subdirectory of `app`.

This can be difficult to do manually (remembering what path you're in and how many directories to go back up, etc), but VS Code can also generate and change these imports for you if it's configured to do so.

Navigate to your workspace settings, search for `typescript import` and change the Typescript Import Module Specifier to `relative` from `auto`.

Or, do this in your preference json:

`"typescript.preferences.importModuleSpecifier": "relative"`