# Admin Jones and the Redbrick Mail Stack - Redbrick Blog
![New mail stack](https://blog.redbrick.dcu.ie/img/2020-05-30-mail.webp)

Redbrick has offered users a mail service for almost as long as we have existed. From our best records, our first mail server was set up between 1998 and 1999, back when the Bulletin Board System was our most valued service and people signed up in droves to message their mates. Nowadays, young brickies will probably need to Google what BBS is, but they definitely know about email.

Over the years, the admins have treated the mail stack with utmost respect - refusing to make any changes to it unless absolutely necessary. The result of this constant patch-based maintenance philosophy has been a mail stack so old that it [predates modern vulnerabilities](https://nvd.nist.gov/vuln/detail/CVE-2019-10149) and a horrific spam rating for our domain. It was about time we did something about it.

An Impossible Task
------------------

As of 2019, our existing setup was daunting. Not only were the configurations scattered across multiple servers (we weren’t even sure which host ran Dovecot, our Mail Delivery Agent), but it was so fragile that changes as small as new mail alias were a risky move. Other than user complaints, we had no way of knowing if all parts of the stack were working, and tracking down useful logs was a futile task. Every day admins received about 40 emails from failed cron jobs and mailing list administration requests, and the spam folder was becoming as important as the inbox for finding legitimate mail.

Old admins and members were able to provide some great insight into the history of the stack, but were quick to highlight the issues with any attempt to change how it works or the services we used. It was accepted that this refresh was going to have a net positive even if it meant breaking some legacy processes along the way - but we didn’t want to lose any mail.

Exposition
----------

![The spam mail](https://blog.redbrick.dcu.ie/img/2020-05-30-mails.webp)

Time to get technical (don’t worry, I’ll go back to less technical language after this). What we had was a Ubuntu 14.04 server (called Paphos) running Exim 4.82 and Dovecot 2.2.9 - the latter released in _November 2013_. We were using [Maildir](https://en.wikipedia.org/wiki/Maildir)\-based mail storage which was written to each user’s home directory on our network storage. Any users that wanted to read their Redbrick mail could use [mutt](http://www.mutt.org/) via SSH login, or our [Rainloop webmail portal](https://webmail.redbrick.dcu.ie/). It wasn’t possible to connect an external mail client to the system to send mail for…some reason. Our spam filtering was handled by Spam Assassin, and Mailman 2 took care of (well, tried to) mailing lists + archives. Finally, the vast majority of users were using `~/.forward` files to forward their mail to their DCU address, and since this is the default behaviour for new accounts most users simply expected this to continue to work.

On top of simply upgrading all the components, there were a couple of big steps we would have to take that were obvious from the start:

*   Mailman had been rewritten between 2 and 3, and they had a [massive document](https://docs.mailman3.org/en/latest/migration.html) on the update process.
*   We needed to switch to our auto renewed Let’s Encrypt certificates to reduce the work on admins to maintain the services, since we already used them for our web infrastructure.
*   Reducing the number of files in each Maildir would be nice, and so moving to Dovecot’s [MDBox format](https://wiki2.dovecot.org/MailboxFormat/dbox) would be a great improvement.
*   Spam filtering needed to work on forwarded mail, and the `~/.forward` files needed to keep working. Adding [SRS](https://en.wikipedia.org/wiki/Sender_Rewriting_Scheme) would help too.

Did you hear we use NixOS?
--------------------------

At the time this kicked of, the elected admins were evaluating solutions for “Redbrick 2.0” - a total redeployment of our stack with managed configuration making it easier for future admins to get stuck in, maintain and improve our infrastructure. After a couple of weeks of prototyping, it was concluded that the way forward for us would be to use [NixOS](https://nixos.org/nixos/). This will be a blog post on its own some day, but the important thing to note for now is that in order to truly “update” the mail stack it was going to be written in Nix, and that meant porting all the ancillary services too (such as DNS).

CTRL+N
------

Rebuilding of the mail stack started in [June 2019](https://github.com/redbrick/nix-configs/commit/9b944b03758a38fdedc52308ba684be71776c587), and wasn’t finished and deployed until [April 2020](https://github.com/redbrick/nix-configs/pull/53). Those were 10 hard months of work. Was it worth it? Absolutely.

The first step was to set up a sandboxed clone of Redbrick. A new VM was configured on one of our hosts and set up with the domain `redbricktest.cf`. We have TWO /16’s of address space on the internet so setting up a new domain on our infrastructure was trivial.

Once that was done, it was easy to iterate on the conversion of the stack, starting with deploying [Postfix](http://www.postfix.org/). Postfix was the popular choice when admins (new and old) gave their 2 cents on whether we should use Exim again. Its documentation is extremely thorough, and since it has been around for so long it was quite easy to find solutions to problems encountered along the way.

That’s not to say it was easy to learn how to configure it, and do so accounting for all the complexities of un-trusted users on shared infrastructure. I’ve become something of an expert on [client restrictions](http://www.postfix.org/postconf.5.html#smtpd_client_restrictions) at this point, and I don’t know if that’s going to be very useful in the future.

We must give a shout out to a tool called [Swaks](http://www.jetmore.org/john/code/swaks/). It was an absolutely essential tool for testing things throughout this process. Here’s an example of using it to ensure our Mailman account could spoof mail (and failing).

```
swaks --to mctastic@redbricktest.ml --from m1cr0man@redbricktest.ml --auth PLAIN --auth-user mailmgr@redbricktest.ml --server mail.redbricktest.ml --port 587 --tls
=== Trying localhost:587...
=== Connected to localhost.
... lots of SMTP stuff ...
 ~> AUTH PLAIN
<~  235 2.7.0 Authentication successful
 ~> MAIL FROM:<m1cr0man@redbricktest.ml>
<~  250 2.1.0 Ok
 ~> RCPT TO:<mctastic@redbricktest.ml>
<~* 554 5.7.1 <localmail.redbricktest.ml[127.0.0.1]>: Client host rejected: Access denied
 ~> QUIT
<~  221 2.0.0 Bye
=== Connection closed with remote host.

```


Next up was moving [Dovecot](https://dovecot.org/) to Nix. This wasn’t any easier than deploying Postfix - our configs were a mess so we just rewrote them from scratch adopting any best practices we could find along the way. When it came to migrating the mail to mdbox, it was as simple as running this command:

```
dsync mirror -A -1 maildir:~/Maildir

```


It did take 8 hours to run, but what do you expect with 20 years of history, and all that Cron spam admins got?

The Mailing Lists
-----------------

At one point in time, Redbrick offered annoucement mailing lists for every society in DCU. That’s no longer the case, and most people use other SaaS solutions, but not us of course!

Deploying Mailman 3 was definitely the most difficult part of the process. It is a [Django](https://www.djangoproject.com/) app, which should say a lot. It’s akin to saying it’s an [Electron](https://www.electronjs.org/) app, if you’re familiar with that genre of software.

This post is already too long for me to rant much about it, but in the end it took about 3 months to get it authenticating users, sending/receiving mail and sending administration mails. Migrating mail meant going so far as to hot patch Mailman’s migrations scripts to deal with really badly broken headers in some of our oldest archives. If you are curious, you can see our entire migration process [here](https://gist.github.com/m1cr0man/d027bdb8c34c1cdb92b5fb0f81b45d02), which includes the patch files.

Again, it was worth it, since we now have Redbrick announce [dating back to 1999](https://lists.redbrick.dcu.ie/hyperkitty/list/announce-redbrick@lists.redbrick.dcu.ie/1999/11/). There are over 380 lists in our archive, and if you happen to be reading this and would like to see the announcements for some old society get in contact with us!

Security, Speed, Simplicity. Choose three
-----------------------------------------

![Good headers on new mail](https://blog.redbrick.dcu.ie/img/2020-05-30-headers.webp)

Making the new mail stack secure was the most important goal for the rebuild. Historically we’ve had a lot of issues around users spoofing mail from DCU addresses, in particular high profile people, and we’ve had no way to track who it was. We also get a LOT of spam since unfortunately a lot of users use their Redbrick address as their burner address.

Postfix will now only send mail for our domain if the user is properly authenticated. They cannot spoof their sender address (unless they have an alias configured), and any incoming mail from our domain is dropped (no one should be _sending_ Redbrick mail except our servers). This makes it impossible to spoof our addresses, and it allows us to trace users in our logs to see who sent a specific mail.

We also spam filter incoming _and_ outgoing mail. As mentioned earlier, we can’t inherently trust users inside our network. We also set up Dovecot sieve scripts to train the spam filter. If a user moves mail in or out of their spam folder, it will be recorded as spam or ham respectively. This has worked wonders, and we’re seeing a large portion of spam filtered on the basis of these rules.

![Rspamd stats](https://blog.redbrick.dcu.ie/img/2020-05-30-rspamd.webp)

Finally we added all the usual standards for signing our outgoing mail and looking like a real domain. We have valid SPF, DKIM, and DMARC records for all mail, and any mail forwarded through us has SRS applied appropriately. This prevents Gmail marking email from us or through us as spam. Over time this should heal our spam rating too.

You would think with all this processing it would take some time for our one mail server to send and receive mail - but no! Even with all this, and with the help of the improvements from using MDBox on network storage, we can process mail in just **three seconds**.

Why not use Gmail?
------------------

One of the reasons people might be inclined to use a service like Redbrick over simply using Gmail is trust. We are very transparent about [how our stack works](http://docs.redbrick.dcu.ie/mail/), and we do our best to keep it secure. We also spam filter with no exceptions for marketing or advertising, and you have full control over your email. We also provide a lot of services for those that tap into them, like advanced sieve scripting for organising your mailbox and mailing lists on requests.

We do now provide public SMTP and IMAP ports too so you can connect from [any device](https://wiki.redbrick.dcu.ie/index.php/Mail), and we already have a number of users connecting through their smartphones and local mail clients.

Finally, the experience of running a mail server is essential for anyone looking to go down the career path of system operations. Giving our elected admins an opportunity to maintain and run a stack like this for a user base that only _sometimes_ shouts about uptime is invaluable and not something you will get from any internship.

Hopefully people can start to enjoy Redbrick mail and use it for what it was originally intended for - socialising! We admins have already started some new mailing lists to discuss future changes, and we have received positive feedback from many users on the reduced spam.

m1cr0man && The admin Team