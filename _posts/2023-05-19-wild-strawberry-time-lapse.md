---
layout: post
title: "Time-lapse of Wild Strawberry Plants over 4 Months"
date: 2023-05-19
---

I got a smart garden from work last Christmas and I was very excited to try it out. At that point I have a haworthia zebra plant, a begonia maculata, a mango plant, and an avocado plant. I started the last 2 plants from seed, and, no, I don't expect them to bear any fruit. I really wanted to try growing something that I can consume later, so I ordered a packet of wild strawberry pods. I also got a raspberypi camera and made a timelapse video.

[![demo_video]({{site.url}}/img/wild_strawberry_youtube_thumbnail.png)](https://youtu.be/htmcwLssEDY)

Here's my set up,
1. write python script to take a picture
2. schedule python script to run every 30 minutes
3. wait for 4 months...and make a time lapse using `ffmpeg`!
4. add some music to the video


## Code snipet to take 1 picture with raspberipi camera:

```py
#!/home/pi/.pyenv/shims/python3
from time import sleep
from datetime import datetime

from picamera import PiCamera
import pytz

camera = PiCamera()
camera.resolution = (1024, 768)
camera.start_preview()
# Camera warm-up time
sleep(2)
filename = datetime.now(pytz.timezone('US/Eastern')).strftime('%Y%m%d%H%M%S')
camera.capture("/home/pi/camera/" + filename + ".jpg")
```

## Crontab entry to execute the python code above every 30 minutes:

```
# every 30 minutes
*/30 * * * * /home/pi/camera/timelapse.py
```

## Steps to install ffmpeg and make timelapse:

- I followed [this](https://www.hostinger.com/tutorials/how-to-install-ffmpeg) detailed instructionto install ffmpeg
- I followed [this](https://medium.com/@sekhar.rahul/creating-a-time-lapse-video-on-the-command-line-with-ffmpeg-1a7566caf877) blog post to make the video. TL;DR: use `ffmpeg -framerate 30 -pattern_type glob -i "./*.jpg" -s:v 1440x1080 -c:v libx264 -crf 17 -pix_fmt yuv420p wildstrawberry.mp4`

## Adding music

Ever watched a movie that sxxk axx but has great soundtrack? To make the timelapse more interesting watch, I used [this uplifting piece](https://uppbeat.io/track/roger-gabalda/a-little-peace) from [uppbeat.io](uppbeat.io) as the background music. The free account allows for 3 downloads per month. 

Then I used iMovie to combine everything and export the final video ready for upload!

The resolution of the video is not great, and you can't see the strawberries very clearly. So here's a picture of my last harvest.

![wild_strawberries.jpg]({{site.url}}/img/wild_strawberries.jpg) 

I still have a packet of cherry tomato pods, and a packet of moss rose pods. I can't wait to try them out later this year 😆
