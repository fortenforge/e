---
layout: post
title:  "Anatomy of a Man-in-the-Middle Phishing Campaign on Coinbase"
date:   2018-05-28 3:12:44 -0500
---

In the summer after my freshman year of college, I had an internship with a startup that tries to defeat phishing attacks against enterprise corporations. As such, I spent a lot of time staring at phishing sites. Once, I showed a phishing campaign against MIT's OWA email to my boss, and he blew my mind when he dug through the site and managed to find a publicly-accessible text-file where the attackers were logging all the password attempts.

Ever since then I've been trying to replicate that same experience on every phishing attempt that comes my way, and for the first time since, I managed to do it! The attack was targeting [Coinbase](https://www.coinbase.com/), probably the most popular site for buying and selling cryptocurrencies. I'll first describe the (fairly sophisticated) campaign, and then show how I discovered the password log file.

<!--more-->

## The attack

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

Anyways, the phishing site was good enough to fool plenty of people as we'll see later. When you enter an email and password, the site *actually tries to log-in* on the real Coinbase website, and throws up an error if your credentials aren't valid. Most phishing attempts are purely passive: the site will log your username and password when you try to sign in, and then will redirect you to the real site. However, since Coinbase enables 2-factor authentication for all its users, a username and password won't get the attackers very far.

Instead, the attackers must launch a *man-in-the-middle*, or *relay* attack, in which they sit in between the user and the real Coinbase.com site, and pass data back and forth between the two.

Once a user gives a valid email and password pair, Coinbase will ask for a six digit code from the user's phone. So, the phishers will ask for the code as well:

[![]({{ site.baseurl }}/assets/phishing_coinbase/step2.png)]({{ site.baseurl }}/assets/phishing_coinbase/step2.png)

Once users enter the code, the phishing site forwards it to Coinbase. However, Coinbase still has one last defense: since the attackers are logging in from a new IP and device, Coinbase will send an email to the user and ask them to confirm the log-in attempt by clicking on the link.

[![]({{ site.baseurl }}/assets/phishing_coinbase/real_device_confirmation.png)]({{ site.baseurl }}/assets/phishing_coinbase/real_device_confirmation.png)

Furthermore, this link must be clicked by the same device that the user is trying to log-in on:

[![]({{ site.baseurl }}/assets/phishing_coinbase/email.png)]({{ site.baseurl }}/assets/phishing_coinbase/email.png)

So, how do our phishers get around this? Ingeniously, they actually ask that the user *paste the url of the confirmation link* in their form:

[![]({{ site.baseurl }}/assets/phishing_coinbase/device_confirmation.png)]({{ site.baseurl }}/assets/phishing_coinbase/device_confirmation.png)

That way, the attackers can access the page from the device they're trying to sign-in on, and Coinbase will never know the difference. Once you paste the link, the attackers will redirect you to [coinbase.com](https://coinbase.com) and drain all the balance from any cryptocurrencies accounts you have.

## Digging through the site files
