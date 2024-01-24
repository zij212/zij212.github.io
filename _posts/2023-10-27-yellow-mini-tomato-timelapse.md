---
layout: post
title: "Timelapse of Yellow Mini Tomato Plants over 5 Months"
date: 2023-10-27
---

In the [previous post]({{site.url}}blog/2023/05/19/wild-strawberry-time-lapse) I wrote about growing wild straberry plants in the Click and Grow Smart Garden 3. After moving the strawberry plants outdoors in mid summer, I decided to start growing mini tomatoes.

And here's 5 months of tomato growth!

[![demo_video]({{site.url}}/img/yellow_mini_tomato_youtube_thumbnail.jpg)](https://youtu.be/P59vHrff5Eo)

I used the same setup as last time. You can read about the details [here]({{site.url}}blog/2023/05/19/wild-strawberry-time-lapse). The only difference is that I used `ffmpeg` to combine the video and audio this round because I noticed that iMovie degrades video resolution.

This is the command to combine the video and audio:

```sh
ffmpeg -i cherry_tomato.mp4 -i fall-luke-moseley-main-version-03-49-10705.mp3 -c:v copy -c:a aac -map 0:v:0 -map 1:a:0 cherry_tomato_with_audio.mp4
```

Happy growing!
