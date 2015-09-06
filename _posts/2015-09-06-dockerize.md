---
layout: post
title:  "Dockerize Crazy Fruit Market"
date:   2015-09-06 16:20:00 +0800
---

I've stopped developing Crazy Fruit Market for several months, but there is 1 thing keeps haunting me -- is there any chance that somebody wants to rebuild this project but fails to do so due to my poor documentation? Hmm... maybe nobody tries to rebuild the project until I'm dead, but what if?

Becuase of this what-if, I decide to make a fool-proof installation guide -- "Dockerize" the project! Recently Docker becomes so hot because of its containerization magic. It eases the pain of deploying the code on different environments, but at the same time it is lighter than virtual machine. Therefore, I decided to try to build a Docker image for Crazy Fruit Market, and here comes the hell of `.bashrc` .

## .bashrc hell

I always thought I was the guy who quite familiar with Linux Bash than most of developers, but this time I found I was wrong. For a long time, we've been living in the place that OS has already taken care many things for us, such as `/etc/profile`, `~/.bashrc`, etc. So, everytime we login the server, many environment variables has already been set up for us.

However, Dockerfile is definitely not this case. Every `RUN` command in a Dockerfile is totally a new entry command (I don't know what it actually calls) and it won't `source` any rc file for you. Although it still works without sourcing rc files in most cases, Cocos2d-js crashes. It requires you to source your `~/.bashrc` before executing its tool commands. You may say, "well, we can just source them in the 1st `RUN`, right?".  No! As I said, every `RUN` command is a new entry command, which means you will have to `source` the rc file in every `RUN`.

What's worse, `source` doesn't work in this way:

{% highlight bash %}
# Why so ugly? because you can't source in /bin/sh
RUN bash -l -c "source ~/.bashrc \
    && cocos compile -p web -m release"
# this returns `cocos not found` in command!!
{% endhighlight %}

Although `source` exectutes the content of file in the current shell, it still cannot pass the environment variables to the next immediate command.

Finally, I gave up writing an elegant Dockerfile and added 3 `ENV` to deal with this issue. The cost of doing so -- it adds additional 3 layers in this Docker image.

## Fat Image

The image gets so clumsy is mainly because the huge size of cocos2d-js (around 800 MB). Oh... so sorry for anyone who wants to pull this fat image. I should remove some unused parts in cocos2d-js. But again, trials and errors come. Exausted with these Dockerfile issues in this weekend , I think I won't do this until next week.

## Final words

We should be grateful for every success in 3rd party package installation.

We should be grateful for those crystal clear documentation.

We should be grateful for those convenient package management tools.

We should be grateful for those available Docker images, because each line written in Dockerfile kills tons of brain cells from an anonymous guy!