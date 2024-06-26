---
title: Beautifying Your Terminal - Custom CLI Commands
created: 2024-03-23T00:00:00
modified: 2024-04-04T02:07:00
tags:
  - linux
  - shell
  - terminal
  - beauty
author: browner
---

Hey Brickies!

As computing students, we all know that the best way to do anything on a PC is via the command line *(how else would people know that you're in IT?!)* And as Redbrick members, you are aware that you have a free to use wetty environment? ([wetty.redbrick.dcu.ie](https://wetty.redbrick.dcu.ie))

But while looking like a l33t hacker is great while you're doing God-knows-what in your terminal, sometimes that black and white window is just so… bland. 

So, in this blog, let's explore some things you can do to make your terminal less boring, and in certain cases, more useful! 

Lets start off with some prerequisites, then we can mix and match them to make some cool terminal displays…

## `figlet` Command

The `figlet` command is a handy [ASCII](https://en.wikipedia.org/wiki/ASCII) text generator, that turns the text you put in into an ASCII design, ready for you to copy and paste! Make some cool designs and say what you like with a cool emphasis! Once you install by entering `sudo apt install figlet`, you can call it by simply entering: `figlet your_text_here`. If all goes well, you'll have something like this:

```
 _   _  ___  _   _ _ __| |_ _____  _| |_    | |__   ___ _ __ ___
| | | |/ _ \| | | | '__| __/ _ \ \/ / __|   | '_ \ / _ \ '__/ _ \
| |_| | (_) | |_| | |  | ||  __/>  <| |_    | | | |  __/ | |  __/
 \__, |\___/ \__,_|_|___\__\___/_/\_\\__|___|_| |_|\___|_|  \___|
 |___/             |_____|             |_____|
```

While `figlet` is a cool,easy-to-use command to get some really quick ASCII designs, it is only limited to text. You can use some other resources to get pictures/graphics ASCII-ified, such as [manytools.org](https://manytools.org/hacker-tools/convert-images-to-ascii-art/). Here, you can take a certain logo that you like, edit how large or small it is, and you have your very own text-ified logo, like you would see in some cool hacker movie/videogame. More on that later…

## `lolcat`

With the `lolcat` command, you can make your text into a rainbow! For example, you can type `lolcat file/path` and it will return that file's contents in rainbow style! So, if I were to (for some reason) have an ASCII art of Hello Kitty in my system, it would look something like this:

![ascii kitty being rainbow-coloured in my terminal, thanks to lolcat. ](../res/ascii-kitty.png)

You can also set cool flags that enhance the `lolcat` function, such as animations (hint: `-a [duration]` :D) Check out `lolcat -h`, explore the different functions, mess around with it a little! 

## `cowsay`

This might be the silliest command, but you can use `cowsay` to have a cute little cow to talk to you in your CLI. Nothing fancy here, but awwwwww just look at the little guy (and he has so much to say, hear him out : ))

```
_______
< hello >
 -------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

## A Word on custom/changing Bash Commands

Now, while some of these suggestions do not help you out in terms of productivity, let's have a look at one that can actually help you navigate your CLI a little better.

By editing the `.bashrc` file, we can add our own custom `alias` to commands, to make the terminal easier to use. Let's say you are sick of typing out the same commands that you use frequently in your CLI, such as `ls -a`. This command lists all items in a current directory, including hidden `.filename` files. But typing out l, then s, then -, then a, AND a space in between them?! Don't be ridiculous, that's just too much work, especially if you use this command over and over. Let's shorten that in a memorable but functional way. `ls -a` can be shortened down to `la` by using the command:

```bash
alias la="ls -a"
```

Add this command to the BOTTOM of your `.bashrc` file (type `cd`, press enter, then type `nano .bashrc` and scroll to the bottom of the file, then add it in there.) After you add it to `~/.bashrc`, `Ctrl-S`, `Ctrl-X` and give it a shot!

**Tip**: You can rename it to anything you like that isn't already a command installed on your OS. So get creative with the `alias` command, or be efficient, or both!

## `neofetch`

Now we can get in to some more detailed commands. `neofetch` is a handy command that shows you some cool metrics about the device you are using. Maybe you know this info already, but `neofetch` is just a handy and aesthetic looking way to quickly access it. Just type `neofetch` when you have it installed. Here's what it looks like: 

```
       _,met$$$$$gg.          user@hostname
    ,g$$$$$$$$$$$$$$$P.       -----------
  ,g$$P"     """Y$$.".        OS: Name
 ,$$P'              `$$$.     Host: Name
',$$P       ,ggs.     `$$b:   Kernel: Name
`d$$'     ,$P"'   .    $$$    Uptime: 1 hour, 40 mins
 $$P      d$'     ,    $$P    Packages: 530 (dpkg)
 $$:      $$.   -    ,d$$'    Shell: bash 5.2.15
 $$;      Y$b._   _,d$P'      Resolution: 1920x1080
 Y$$.    `.`"Y$$$$P"'         Terminal: path
 `$$b      "-.__              CPU: Name
  `Y$$                        GPU: Name
   `Y$$.                      
     `$$b.                    Memory: xMiB / xMiB
       `Y$$b.
          `"Y$b._
              `"""
```

You can even change it so that you have a custom design on it. The default is your OS's logo, such as Debian as showed above, but by defining it in the command you run, you can change the logo to any ASCII file you have saved (remember the ASCII image converter from earlier?). Just run `neofetch --ascii path/to/ascii` and run (oh, and  `| lolcat` works too!)

## OK, Practice time

So, let's put this all together, and create a cool custom startup animation…thingy. By editing the `.bashrc` file we talked about earlier, we can make it so that, on login, a bunch of the previously mentioned commands will display. So, `nano .bashrc` and let's get started. All of the commands we use will be available in `wetty`, so no need to `apt install` anything new. 

In `.bashrc`, there are some important things defined already, so navigate to the end of the file and make a blank line. Start with a `clear`, so that no other login text displays; this leaves a blank slate. Next, we can create a new line, and put a Welcome message in at the top using `figlet your_text_here`. I like to just put my Redbrick username as I might have other tabs open, so it allows me to quickly see what instance/account I am ssh'ed into. 

```
      _       ___
 _ __| |__   / / |__  _ __ _____      ___ __   ___ _ __
| '__| '_ \ / /| '_ \| '__/ _ \ \ /\ / / '_ \ / _ \ '__|
| |  | |_) / / | |_) | | | (_) \ V  V /| | | |  __/ |
|_|  |_.__/_/  |_.__/|_|  \___/ \_/\_/ |_| |_|\___|_|
```

Make a new line, and let's throw in a `neofetch` with a custom ASCII. Find a logo you like (I was able to make a Redbrick image into ASCII, provided below for your use,) and convert it to ASCII - don't make the logo too big or it won't fit in the `neofetch`. Then save it as a file on your instance, then pass it to your `neofetch` command with the `--ascii` flag as before.

```
                                    ###
                              ##############             
                         #######################            
                   #################################        
             (##(###############(###############(#######
        ###########################################    ##   
      #######################################    ########   
    ###.   #############################   ##############   
    ########   #################(#    ###################   
    ############   #########    #########################   
    ################.      ##############################   
    ##################  #############################       
     ###(#######(#####  (#######(#######(######             
         #############  ##################
             #########  ############
                  ####  ######
                        #
```

How about a little `cowsay`, too? Do a new line, and have him say whatever you like, go nuts!

```
 _________________________________________
< ohhhh BROTHER JUST END THE BLOG ALREADY >
 -----------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

OK, OK, I'll leave the rest up to you guys. I had a lot of fun trying to figure out different animations and customisations that I could do, and managed to get a cool looking, Redbrick themed loading screen working, too. I reckon you can get it pretty easily!

<video src="../res/wetty.mp4" width="400px" height="300px" alt="browner's wetty loading screen, featuring a brick loading animation"></video>

## Other Cool Commands

There are a couple of other helpful commands that I cannot really go into detail about, but might be worth checking out. One of these is `tmux`, which allows you to split your CLI into multiple instances and swap between them, which is really helpful for keeping things neat and organised. There is a bit of a learning curve to learning how to use `tmux`, but there are plenty of online resources to show you how to use it

`hollywood` starts an animation that looks straight out of a 90's movie where a computer is in the shot. Nothing else really to it, so I just left it as a little honourable mention. `cmatrix` is also a similar command, but for some Matrix visuals instead, in case you didn't catch that. 

`sl` makes a cool train animation slide across your screen. If you did an admin exam recently, you may be reminded of the torture of that 24 hours as the steam locomotive chugs by. 

`fortune` can be added to the likes of `figlet` and `cowsay` and gives a random quote/saying/anecdote every time it is run. There are a few different flags you can do, but the main use is just a random sentence or two. 

```bash
client:~$ fortune
It's all in the mind, ya know.
```

Anywho, now that you have a few commands, as well as some experience editing `.bashrc`, go forth and do some more customisation of your own! Make your CLI that little bit less boring. If you have any suggestions for other commands that folks can use, please leave a comment down below!

Signing off,

`browner`
