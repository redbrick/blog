---
title: Getting Started With Your Redbrick Webspace!
created: 2024-03-25T15:00:00
tags:
  - hackerclub
  - tutorial
  - website
  - beginner
author: fbaxter
---
Hey Brickies,

So the internet huh? All them websites, am I right? Well Redbrick gives every member their own dedicated webspace, even you! This article will cover an extremely asked about, and largely undocumented, Redbrick service that is beyond easy to setup.

## Why?
You must be asking: "Why would I use this **extremely awesome** and **free** service?" Well allow me to clue you in. Redbrick webspaces are extremely easy to setup, with just a few simple commands to get you up and running. They are also a great way to host a variety of different (static) sites.

## How?
So how do you make use of this great service? Well first you need a site to use. A great first site to use is the portfolio you created for CA106 (Web Design). You could also develop something else, either on your own system or directly on Redbrick server via SSH. For the sake of this tutorial we will assume a locally developed website to be pushed to your webspace.

So first you pick and SFTP client of your choice. Most either gravitate to either [WinSCP](https://winscp.net/eng/index.php) or [FileZilla](https://filezilla-project.org/). I myself prefer WinSCP. The information you need to affectively connect to Redbrick servers with clients is shown bellow:

```
Hostname: pyg.redbrick.dcu.ie
Username: your_redbrick_username
Port: 22
```

You will be prompted for a password and once you are logged in you should see a similar file structure to this:
```
-Maildir
-public_html
-{any other folders you have created}
```
Note these may not be in the same order.

The folder we are concerned with is `public_html`. Click on this to enter your webtree space. 

Now you can begin to transfer the files required for your website. Keeping in mind that your `index.html` should be in the root of you `public_html`. Other pages can be put in other folders as you please, but the index needs to be placed like this to ensure your website works.

Once you have all of your files uploaded you need to ssh onto Redbrick services to make some permission changes. The easiest way to do this is to go to https://wetty.redbrick.dcu.ie and enter your login details. 

You can also ssh normally by opening a terminal/cmd and entering `ssh your_redbrick_username@pyg.redbrick.dcu.ie` and providing your password when prompted.

Once you are logged in you need to run two commands as shown bellow:
```
chmod 755 public_html
chmod 644 public_html/*
```

Now you can go to `https://your_redbrick_username.redbrick.dcu.ie` and see your amazing website!

## Potential Issues
The two main issues you can encounter are 403 or 404 errors.

To resolve the 403 error you need to ensure you've run the two chmod commands that were shown above, especially if you've replaced files or added new ones. 

For 404 errors you need to make sure that the site structure follows something similar to bellow. Particularly the placement and naming of `index.html`.
```
public_html
	index.html
	{other_files}
```

For any other issues you may have, you can message in `#helpdesk` in the discord server.

## Outro
Hopefully now you have your very own Redbrick webspace that you can show off to your friends and ~~shill Redbrick~~ let them know how than can have their own!

If you have any issues/questions drop a message in `#helpdesk`.
