##markdown

Hey Brickies!

As computing students, we all know that the best way to do anything on a PC is via the command line (how else would people know that you're in IT?!) And as Redbrick members, you are aware that you have a free to use wetty environemnt? (wetty.redbrick.dcu.ie)
But while looking like a l33t hacker is great while you're doing God-knows-what in your terminal, sometimes that black and white window is just so... bland. 
So, in this blog, let's explore some things you can do to make your terminal less boring, and in certain cases, more useful! 

Lets start off with some prerequisites, then we can mix and match them to make some cool terminal displays...


 ## figlet command

The `figlet` command is a handy [ASCII](https://en.wikipedia.org/wiki/ASCII) text generator, that turns the text you put in into an ASCII design, ready for you to copy and paste! Make some cool designs and say what you like with a cool emphasis! Once you install by entering `sudo apt install figlet`, you can call it by simply entering: `figlet your_text_here`. If all goes well, you'll have something like this:

![Figlet command example](res/figlet screenshot.png)

While `figlet` is a cool,easy-to-use command to get some really quick ASCII designs, it is only limited to text. You can use some other resources to get pictures/graphics ASCII-ified, such as [manytools.org](https://manytools.org/hacker-tools/convert-images-to-ascii-art/). Here, you can take a certain logo that you like, edit how large or small it is, and you have your very own text-ified logo, like you would see in some cool hacker movie/videogame. More on that later...

## lolcat

With the `lolcat` command, you can make your text into a rainbow! For example, you can type `lolcat file/path` and it will return that file's contents in rainbow style!

(proof)

You can also set cool flags that enhance the `lolcat` function, such as animations (hint: `-a [duration]` :D) Check out `lolcat -h`, explore the different functions, mess around with it a little! 
...

## cowsay

This might be the silliest command, but you can use `cowsay` to have a cute little cow to talk to you in your CLI. Nothing fancy here, but awwwwww just look at the little guy (and he has so much to say, hear him out : ))

(proof)

## A Word on custom/changing bash commands

Now, while some of these suggestions do not help you out in terms of productivity, let's have a look at one that can actually help you navigate your CLI a little better.

By editing the .bashrc file, we can add our own custom `alias` to commands, to make the terminal easier to use. Let's say you are sick of typing out the same commands that you use frequently in your CLI, such as `ls -a`. This command lists all items in a current directory, including hidden `.filename` files. But typing out l, then s, then -, then a, AND a space in between them?! Don't be ridiculous, that's just too much work, especially if you use this command over and over. Let's shorten that in a memorable but functional way. `ls -a` can be shortened down to `la` by using the command: 

`alias la="ls -a"`

Add this command to the BOTTOM of your .bashrc file (type `cd`, press enter, then type `nano .bashrc` and scroll to the bottom of the file, then add it in there.) After you add it to .bashrc, CtrlS, CtrlX and give it a shot! 

Tip: You can rename it to anything you like that isn't already a command installed on your OS. So get creative with the `alias` command, or be efficient, or both! 

## neofetch
Now we can get in to some more detailed commands. `neofetch` is a handy command that shows

## OK, Practice time!

##Other cool commands
hollywood, tmux



