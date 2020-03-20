---
title: "FFmpeg exited with code 1, Homebridge and Homekit configuration with Axis camera"
description: "When connecting your IP camera to Homekit, you may have run into issues with ffmpeg exiting with code 1 when trying to stream."
date: 2020-01-19T10:00:14-08:00
---

## The Problem

If you're trying to use the [homebridge-camera-ffmpeg](https://github.com/KhaosT/homebridge-camera-ffmpeg) plugin for homebridge to connect your IP camera to Homekit, you may have run into issues with ffmpeg exiting with `code 1` when trying to stream. This usually means ffmpeg can't launch with the options provided in your camera configuration, but many different things can be going wrong and it's hard to debug.

{{< highlight txt >}}
[1/18/2020, 8:27:54 PM] [Camera-ffmpeg] Snapshot from Front door at 480x270
[1/18/2020, 8:27:56 PM] [Camera-ffmpeg] Start streaming video from Front door with 1280x720@299kBit
[1/18/2020, 8:27:56 PM] [Camera-ffmpeg] ERROR: FFmpeg exited with code 1
{{</ highlight >}}

There are lots of ways this can go wrong, so here are some steps to figure out where you might be having issues.

## The Solution

First, confirm `ffmpeg` is installed and runs on your homebridge server. Just run `ffmpeg` at the command line and confirm it runs. Here's what running it successfully looks like:

{{< highlight txt >}}
ffmpeg version 2.8.15-0ubuntu0.16.04.1 Copyright (c) 2000-2018 the FFmpeg developers
  built with gcc 5.4.0 (Ubuntu 5.4.0-6ubuntu1~16.04.10) 20160609
  configuration: --prefix=/usr --extra-version=0ubuntu0.16.04.1 --build-suffix=-ffmpeg --toolchain=hardened --libdir=/usr/lib/x86_64-linux-gnu... etc
{{</ highlight >}}


You may want to note the codecs that `ffmpeg` has been installed with. For my particular Axis camera, it was important to have h264 support so you'll look for `--enable-libx264`.

Next, you need to make sure you have the right video and image source URLs for your axis camera. There are quite a few variations. Here is how the full configuration looks:

{{< highlight json >}}
{
  "platform": "Camera-ffmpeg",
  "cameras": [
    {
      "name": "Front door",
      "videoConfig": {
        "source": "-rtsp_transport tcp -i rtsp://user:pass@1.2.3.4/axis-media/media.amp",
        "stillImageSource": "-i http://1.2.3.4/jpg/image.jpg?size=3",
        "maxStreams": 2,
        "maxWidth": 1280,
        "maxHeight": 960,
        "maxFPS": 30,
        "vcodec": "h264"
      }
    }
  ]
}
{{</ highlight >}}

Both `source` and `stillImageSource` urls can be looked up on [this axis endpoint chart](https://www.ispyconnect.com/man.aspx?n=Axis). Note that you need to add a username and password in the URL if configured, and of course substitute your own camera IP in for `1.2.3.4`.

Lastly, if you still can't figure out what's going wrong, enable debug mode for your `homebridge-camera-ffmpeg` source and get more information:

{{< highlight json >}}
...
  "maxFPS": 30,
  "vcodec": "h264",
  "debug": true
...
{{</ highlight >}}

This will give you more info about what the plugin sees from your camera and what the result of the ffmpeg call is when trying to fetch the stream. You should attempt to look at the video stream in Homekit to kick off the ffmpeg process.