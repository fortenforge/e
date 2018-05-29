---
layout: post
title:  "Anatomy of a Man-in-the-Middle Phishing Campaign on Coinbase"
date:   2018-05-28 3:12:44 -0500
---

In the summer after my freshman year of college, I had an internship with a startup that tries to defeat phishing attacks against enterprise corporations. As such, I spent a lot of time staring at phishing sites. Once, I showed a phishing campaign against MIT's OWA email to my boss, and he blew my mind when he dug through the site and managed to find a publicly-accessible text-file where the attackers were logging all the password attempts.

Ever since then I've been trying to replicate that same experience on every phishing attempt that comes my way, and for the first time since, I managed to do it! The attack was targeting [Coinbase](https://www.coinbase.com/), probably the most popular site for buying and selling cryptocurrencies. I'll first describe the campaign, and then show how I discovered the password log file.

<!--more-->

## The hook

On Wednesday, May 16 at 4:20PM I received the following text message:

![who knows how they got my number]({{ site.baseurl }}/assets/phishing_coinbase/txt_message.png){: .center-image }

Several things should raise your suspicions immediately: first of all, no one is going around handing out 27 BTC (around $226,000 at the time) to random phone numbers. Secondly, the domain name that we're linked to, tx-app.com, is not associated with Coinbase. (Incidentally, the "FRM" address was almost certainly generated through keyboard-mashing: there are no 1's present at all, there are no repeated digits, and there are long strings of ascending or descending sequences like "98765").

When we click on the link we're redirected from tx-app.com to coinbase.tx-app.com and are taken to a website that looks pretty similar to [Coinbase's login screen](https://www.coinbase.com/signin):

[![]({{ site.baseurl }}/assets/phishing_coinbase/home.png)]({{ site.baseurl }}/assets/phishing_coinbase/home.png)

Honestly, it's a much better replica than most phishing attempts I've seen. The one thing that stands out is that the logo in the upper-left corner is not quite at full resolution. The bigger giveaway is the lack of a little green lock icon in the url; because the site is served over HTTP instead of HTTPS and includes a form, instead we see:

![]({{ site.baseurl }}/assets/phishing_coinbase/not_secure.png){: .center-image }

One has to wonder why the phishers didn't bother getting a free certificate from [Let's Encrypt](https://letsencrypt.org/) to make the page more believable. Since the average person doesn't know enough about domain names to understand that coinbase.com is owned by Coinbase, while coinbase.tx-app.com is not, we've entered a dangerous period where anyone can create a reasonably convincing phishing site just by obtaining a certificate.

Thankfully, Chrome is slowly phasing out the green locks entirely, and will actually get even more aggressive with non HTTPS pages [starting in October 2018](https://blog.chromium.org/2018/05/evolving-chromes-security-indicators.html)

![](https://3.bp.blogspot.com/-MkJEkHnXcXc/Wv181DQednI/AAAAAAAAA6E/95MwjxqK7awaCgr_Z6xRNWVi0Ztf0-ncACLcBGAs/s1600/Treatment%2Bof%2BHTTP%2BPages%2Bwith%2BUser%2BInput.gif){: .center-image }

Anyways, the phishing site was good enough to fool plenty of people as we'll see later.

## The phishing attack

Most phishing attempts are purely passive: the site will log your username and password when you try to sign in, and then will redirect you to the real site. However, since Coinbase enables 2-factor authentication for all its users, a username and password won't get attackers very far.

Instead, these phishers launched a *man-in-the-middle*, or *relay* attack, in which they sit in between the user and the real Coinbase.com site, and pass data back and forth between the two. When you enter an email and password, the site *actually tries to log-in* on the real Coinbase website, and throws up an error if your credentials aren't valid.

Once a user gives a valid email and password pair, Coinbase will ask for a six digit code from the user's phone. So, the phishers ask for the code as well:

[![]({{ site.baseurl }}/assets/phishing_coinbase/step2.png)]({{ site.baseurl }}/assets/phishing_coinbase/step2.png)

Once a user enters the code, the phishing site forwards it to Coinbase. However, Coinbase still has one last defense: since the attackers are logging in from a new IP and device, Coinbase will send an email to the user and ask them to confirm the log-in attempt by clicking on the link.

[![]({{ site.baseurl }}/assets/phishing_coinbase/real_device_confirmation.png)]({{ site.baseurl }}/assets/phishing_coinbase/real_device_confirmation.png)

Furthermore, this link must be clicked by the same device that the user is trying to log-in on:

[![]({{ site.baseurl }}/assets/phishing_coinbase/email.png)]({{ site.baseurl }}/assets/phishing_coinbase/email.png)

So, how do our phishers get around this? Ingeniously, they actually ask that the user *paste the url of the confirmation link* in their form:

[![]({{ site.baseurl }}/assets/phishing_coinbase/device_confirmation.png)]({{ site.baseurl }}/assets/phishing_coinbase/device_confirmation.png)

That way, the attackers can access the page from the device they're trying to sign-in on, and Coinbase will never know the difference. Once you paste the link, the attackers will redirect you to [coinbase.com](https://coinbase.com) and drain all the balance from any cryptocurrencies accounts you have.

## Digging through the site files

I started rummaging through the javascript files and noticed that directory listing was enabled:

![]({{ site.baseurl }}/assets/phishing_coinbase/libs.png)

Seeing this, I pointed [DirBuster](https://www.owasp.org/index.php/Category:OWASP_DirBuster_Project) at the site and let it run. DirBuster is a nifty little tool that bruteforces common directory and file names in order to find unintentionally publicly-accessible files on a website. Relatively quickly, it found three new directories: `/data`, `/proxy` and `/vendor`.

The `/vendor` directory contains a bunch of PHP scripts. In particular, it has the full source code for the [PHP Curl Class](https://github.com/php-curl-class/php-curl-class), which the attackers presumably use to make the requests to coinbase.com.

![]({{ site.baseurl }}/assets/phishing_coinbase/vendor.png)

The `/proxy` directory contains two PHP scripts and three text files which list a bunch of IP addresses and associated ports.

![]({{ site.baseurl }}/assets/phishing_coinbase/proxy.png)

Unfortunately, the server tries to run any PHP scripts that are requested, so I couldn't get the source code of these. I suspect that the server was proxying it's requests to Coinbase through these IPs in order to evade blacklisting.

The real jackpot was in `/data`. It contained five different text files, all of which were logs of various parts of the phishing process.

![]({{ site.baseurl }}/assets/phishing_coinbase/data.png)

Three of them (`remove2FA.txt`, `errors.txt`, and `codes.txt`) included a couple of emails and passwords, but mostly just logged errors during various aspects of the Coinbase authentication process. By far the most interesting was `composer.txt` which listed around 300 log-in attempts and included emails, passwords, IP addresses, user agents, and in some cases, cryptocurrency account balances:

[![]({{ site.baseurl }}/assets/phishing_coinbase/composer.png)]({{ site.baseurl }}/assets/phishing_coinbase/composer.png)

Of course, some of these are people trying to login multiple times. I count 132 unique email addresses listed, including obviously fake ones like `hellophisher@hotmail.com`.
