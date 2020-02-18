---
layout: post
title: "Controlling a LED with Google Home and Raspberry Pi"
date: 2020-02-17
---

My friend [Linlin](https://github.com/fanllzz) got me a Google Home for my birthday 2 years ago. Since then I have only used it to play podcasts and music, to check weather forecast or occasionally have it tell jokes to me. I thought it would be nice to use it in a simple project -- turning on and off a LED.


## Preparation 
- A LED
- Some resistors
- 2 pieces of solderless jumper wire
- A breadboard
- A Google Home
- A Raspberry Pi 3


## Circuit

Here is what the circuit looks like. 

![led_circuit.svg](https://github.com/zij212/zij212.github.io/blob/master/img/led_circuit.svg) 

I should have written down on a sticky note that Anode is the positive and longer leg and Cathode is the negative and shorter leg, because I kept forgetting which is which and googled many times. But I guess as long as I remember it’s one-way and use some resistors to protect it, I will be okay.


## Flask app

My application has two methods - on and off, and it is set to run on port 9080. And I used GPIO 26 (PIN 37 if using GPIO.BOARD mode) as the power source. 

```python
#!/usr/bin/python
import RPi.GPIO as GPIO
from flask import Flask

app = Flask(__name__)

GPIO.setwarnings(True)
GPIO.setmode(GPIO.BOARD)
GPIO.setup(37, GPIO.OUT)

@app.route('/on', methods=['GET'])
def on():
    GPIO.output(37,1)
    return 'On'

@app.route('/off', methods=['GET'])
def off():
    GPIO.output(37,0)
    return 'Off'
    
if __name__ == "__main__":
	app.run(debug=True, host='0.0.0.0', port=9080)
```

To run it, run command

```shell
python on_off.py
```

Here is the [map](https://www.raspberrypi.org/documentation/usage/gpio/) of the GPIO pins in case you want to use other pins 

And more [details](https://sourceforge.net/p/raspberry-gpio-python/wiki/BasicUsage/) on how to setup GPIO pins  


## ngrok

I want to expose the application running on my raspberry pi to a public URL and chose to use **ngrok**. 

For more options, read [Tunnelling services for exposing localhost to the web](https://www.chenhuijing.com/blog/tunnelling-services-for-exposing-localhost-to-the-web/#%F0%9F%91%9F) by Chen Hui Jing.

Here are the steps to set it up:

- Go to https://dashboard.ngrok.com/get-started and create an account
- Download Linux(ARM) and scp to raspberry pi
- Unzip file
- Connect to ngrok account
    ```sh
    ./ngrok authtoken your_token_here
    ```
- Serve flask app on port 9080
- Start a HTTP tunnel on port 9080
    ```sh
    ./ngrok http 9080
    ```
    ![ngrok_http.png](https://github.com/zij212/zij212.github.io/blob/master/img/ngrok_http.png) 

And voila! Now I can turn on the LED by visiting https://64b88309.ngrok.io/on and then turn it off using https://64b88309.ngrok.io/off. 


## IFTTT

I want my Google Home to visit the above urls when I say the command “LED on/off”.   

To achieve this, I created two recipes on [IFTTT](https://ifttt.com/) that does the following task:

If this (google assistant) then that (webhook)


## Video

Now that we have everything up and running, here is me demoing the result.

[![demo_video](https://github.com/zij212/zij212.github.io/blob/master/img/led_youtube_thumbnail.JPG)](https://www.youtube.com/watch?v=ZRuIQGlsi-U)


## Thoughts

From this project I learned about GPIO pins, flask apps, ngrok and IFTTT. I can see myself using these tools again in future projects and maybe even send a few messages in a haunted mansion :wink:  

![parasite.jpeg](https://github.com/zij212/zij212.github.io/blob/master/img/parasite.jpeg)   

![parasite.jpeg]({{site.url}}/img/parasite.jpeg) 
