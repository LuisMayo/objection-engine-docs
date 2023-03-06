# objection-engine-docs
Documentation about the objection Engine and the Ace Attorney bots ecosystem

## What is objection engine?
[Objection engine](https://github.com/LuisMayo/objection_engine) is a python library aimed at rendering Ace Attorney-like videos given a list of comments.

## What uses does objection engine has?

### Classic Bots
The "official" uses, this means the uses I personally give the engine are mainly bots. More specifically three bots.

- A Twitter Bot: https://github.com/LuisMayo/ace-attorney-twitter-bot
- A Telegram Bot (now powered off): https://github.com/LuisMayo/ace-attorney-telegram-bot
- A Discord bot: https://github.com/LuisMayo/ace-attorney-discord-bot

### New queue-based bots
Classic bots their advantages
- They were easy to (self)host. They're just python3 apps. In fact the hardest point of hosting them was Objection Engine itself, which requires a GNU/Linux installation

But also their disadvantages
- They consume a good chunk of RAM. This happens because they also have usually two rendering threads.
- They're writen in python which I personally don't like. And given that except the discord bot I have to almost single-handedly maintain the bots myself this becomes too much of a hassle.
- They're hard to escalate

So the plan was to further de-couple the bot logic from the rendering queue logic. So I decided to write a (python) worker which would listen to a RabbitMQ queue and pick up the rendering tasks. While the bots (now written in Typescript) would wait for the worker to reply to the task (using [RPC Pattern](https://www.rabbitmq.com/tutorials/tutorial-six-python.html)) before finally sending the video to the service.

This would be the final picture:

![image](https://user-images.githubusercontent.com/20229636/223217214-08f57d95-43ff-4369-948b-b029e66447ae.png)

This allows me more control over the performance tuning, as I could now dinamically configure the number of workers. It could even let me send the workers to a remote server if I wanted to. To allow horizontal escalation. Something the previous model wouldn't let me. And of course. It freed me from Python :)

So. Following this new model, new repositories arised.

- The worker: https://github.com/LuisMayo/Objection-Engine-Rabbit-Worker
- An opinionated Typescript library to interact with the worker so the bot maintenance would be more streamlined: https://github.com/LuisMayo/objection-engine-deno-client
- A new Telegram Client: https://github.com/LuisMayo/obj-engine-telegram-bot

### Other uses
There are other uses, like an VK bot. But they're not maintained by me.

