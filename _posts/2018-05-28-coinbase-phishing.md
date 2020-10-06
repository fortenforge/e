---
layout: post
title:  "Anatomy of a Man-in-the-Middle Phishing Campaign on Coinbase"
date:   2018-05-29 3:12:44 -0500
tags: security
---

In the summer after my freshman year of college, I had an internship with a startup that tries to defeat phishing attacks against enterprise corporations. As such, I spent a lot of time staring at phishing sites. Once, I showed a phishing campaign against MIT's OWA email portal to my boss, and he blew my mind when he dug through the site and managed to find a publicly-accessible text file where the attackers were logging all the password attempts.

Ever since, I've been trying to replicate that same experience on every phishing attempt that comes my way, and for the first time since then, I managed to do it! The attack was targeting [Coinbase](https://www.coinbase.com/), probably the most popular site for buying and selling cryptocurrencies. I'll first describe the campaign, and then show how I discovered the password log file.

<!--more-->

## The hook

On Wednesday, May 16 at 4:20PM I received the following text message:

![who knows how they got my number]({{ site.baseurl }}/assets/phishing_coinbase/txt_message.png){: .center-image }

Several things should raise your suspicions immediately: first of all, no one is going around handing out 27 BTC (around $226,000 at the time) to random phone numbers. Secondly, the domain name that we're linked to, tx-app.com, is not associated with Coinbase. (Incidentally, the "FRM" address was almost certainly generated through keyboard-mashing: there are no 1's present at all, there are no repeated digits, and there are long strings of ascending or descending sequences like "98765").

When we click on the link we're redirected from tx-app.com to coinbase.tx-app.com and are taken to a website that looks pretty similar to [Coinbase's login screen](https://www.coinbase.com/signin):

[![]({{ site.baseurl }}/assets/phishing_coinbase/home.png)]({{ site.baseurl }}/assets/phishing_coinbase/home.png)

Honestly, it's a much better replica than most phishing attempts I've seen. The one thing that stands out is that the logo in the upper-left corner is not quite at full resolution. The bigger giveaway is the lack of a little green lock icon in the url. Because the site is served over HTTP instead of HTTPS and includes a form, instead we see:

![]({{ site.baseurl }}/assets/phishing_coinbase/not_secure.png){: .center-image }

Anyways, the phishing site was good enough to fool plenty of people as we'll see later.

## The phishing attack

Most phishing attempts are purely passive: the site will log your username and password when you try to sign in, and then will redirect you to the real site. However, since Coinbase enables 2-factor authentication for all its users, a username and password won't get attackers very far.

Instead, these phishers launched a *man-in-the-middle*, or *relay* attack, in which they sit in between the user and the real coinbase.com site, and pass data back and forth between the two. When you enter an email and password, the site *actually tries to log-in* on the real Coinbase website, and throws up an error if your credentials aren't valid.

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

Unfortunately, the server tries to run any PHP scripts that are requested, so I couldn't get the source code of these. I suspect that the server was proxying its requests to Coinbase through these IPs in order to evade blacklisting.

The real jackpot was in `/data`. It contained five different text files, all of which were logs of various parts of the phishing process.

![]({{ site.baseurl }}/assets/phishing_coinbase/data.png)

Three of them (`remove2FA.txt`, `errors.txt`, and `codes.txt`) included a couple of emails and passwords, but mostly just logged errors during various aspects of the Coinbase authentication process. Much more interesting was `composer.txt` which listed around 300 log-in attempts and included emails, passwords, IP addresses, user agents, and in some cases, cryptocurrency account balances:

[![]({{ site.baseurl }}/assets/phishing_coinbase/composer.png)]({{ site.baseurl }}/assets/phishing_coinbase/composer.png)

Of course, some of these are people trying to login multiple times. I count 132 unique email addresses listed, including obviously fake ones like `hellophisher@hotmail.com`.

OK, so over 100 people gave up their email and password to the attackers, but can we tell how many actually had their accounts drained? It turns out, we can. This brings us to the last text file: `success.txt`, in which the phishers logged every time they successfully transferred cryptocurrency out of an account. Amusingly, this happened a grand total of three times:

[![]({{ site.baseurl }}/assets/phishing_coinbase/success.png)]({{ site.baseurl }}/assets/phishing_coinbase/success.png)

As you can see, the attackers managed to exfiltrate 0.0124 ETH and 0.0031 BTC, which at the time was worth around $45 USD. Not particularly impressive.

## blockchain, blockchain, blockchain

One neat aspect of all this that is specific to cryptocurrencies is that we can actually see these transactions happening on the blockchain since the log lists the attacker's Bitcoin and Ethereum addresses. You can checkout the two Ethereum transactions [here](https://etherscan.io/tx/0xf61104ec78ac83ec72a24c4bc2f902366ece46a86f6b7a95c2be81d8308a0582) and [here](https://etherscan.io/tx/0x8d99d4eba3dcc5526f2e8641cc86abf312d0557d3ff24cdc7a3436cb889c7763), and you can see the Bitcoin transaction [here](https://blockchain.info/tx/dccd90eadbac2d623d95319d1d56874de6d81abbca0d098b23aff7c69d8a1d9c). I've also shown the Bitcoin transaction below:

[![]({{ site.baseurl }}/assets/phishing_coinbase/bitcoin.png)]({{ site.baseurl }}/assets/phishing_coinbase/bitcoin.png)

As far as I can tell, the ether quickly gets exchanged with [ShapeShift](https://shapeshift.io/#/coins) and the trail runs cold. Similarly, the bitcoin seems to get run through what seems like a [tumbler](https://en.wikipedia.org/wiki/Cryptocurrency_tumbler). I am by no means an expert in this field, so if someone knows more about blockchain reconnaissance than I do, feel free to let me know what you find.

One last thing of note is that while the phishing site was taken down relatively soon after the initial attack, the [Ethereum address](https://etherscan.io/address/0x8a8048a3273A23A2e79a5Cb6e1A56f7D1C73F947) continues to receive small amounts of currency, suggesting that the phishers are continuing to run the scam on new domain names.

[![]({{ site.baseurl }}/assets/phishing_coinbase/ether.png)]({{ site.baseurl }}/assets/phishing_coinbase/ether.png)

All told, the attackers have received around $415 worth of Ethereum since they started (which, to be honest, is still not very impressive given how much work it must have been to create and run the phishing site).

## Lessons Learned

So, what accounts for the dismal performance of the scam? The attackers had a number of things working against them. First was the choice to serve the site over HTTP instead of HTTPS. On unencrypted sites that include a form, Chrome includes a "Not Secure" message next to the URL. In fact, Chrome will actually get even more aggressive with non-HTTPS pages [starting in October 2018](https://blog.chromium.org/2018/05/evolving-chromes-security-indicators.html):

![](https://3.bp.blogspot.com/-MkJEkHnXcXc/Wv181DQednI/AAAAAAAAA6E/95MwjxqK7awaCgr_Z6xRNWVi0Ztf0-ncACLcBGAs/s1600/Treatment%2Bof%2BHTTP%2BPages%2Bwith%2BUser%2BInput.gif){: .center-image }

One has to wonder why the phishers didn't bother getting a free certificate from [Let's Encrypt](https://letsencrypt.org/) to make the page more believable. Since the average person doesn't know enough about domain names to understand that coinbase.com is owned by Coinbase, while coinbase.tx-app.com is not, we've entered a dangerous period where anyone can create a reasonably convincing phishing site just by obtaining a certificate.

Thankfully, Chrome is slowly phasing out the green locks entirely, making HTTPS the accepted default instead of some special status:

![](https://4.bp.blogspot.com/-NOXiIl70UqI/Wv18vLtJqjI/AAAAAAAAA6A/8vjHdMA4ico5ZT9F_i7O5_Rg2VE534e-ACLcBGAs/s1600/pasted%2Bimage%2B0%2B%25283%2529.png){: .center-image }

The other major factor was Coinbase's two-factor authentication. There's a widespread myth that two-factor authentication can prevent phishing. As we
ve seen, this is untrue---to really stop phishing attacks you need to use a hardware token like a [Yubikey](https://www.yubico.com/) paired with some cryptographic protocol like [U2F](https://www.yubico.com/solutions/fido-u2f/) or [WebAuthn](https://blogs.dropbox.com/tech/2018/05/introducing-webauthn-support-for-secure-dropbox-sign-in/) which binds to the TLS session. Coinbase currently doesn't support any such method. Still, two-factor authentication clearly mitigated the damage, if only by making it so hard for users to login that they stopped trying midway through getting phished.

Finally, the largest determinant was Google's [Safe Browsing](https://safebrowsing.google.com/) initiative which [flagged the site](https://transparencyreport.google.com/safe-browsing/search?url=coinbase.tx-app.com) only a couple hours after the text message was sent to me.

[![]({{ site.baseurl }}/assets/phishing_coinbase/dangerous.png)]({{ site.baseurl }}/assets/phishing_coinbase/dangerous.png)

Once a site has been flagged, Google displays a giant red page to users, warning them that they're about to phished. Once this happens, the attackers have pretty much no hope of extracting that much more currency from additional users.

## `whois` data

```
Domain Name: TX-APP.COM
Registry Domain ID: 2264223847_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.hostinger.com
Registrar URL: https://www.hostinger.com
Updated Date: 2018-05-16T19:42:48Z
Creation Date: 2018-05-16T19:42:47Z
Registrar Registration Expiration Date: 2019-05-16T19:42:47Z
Registrar: Hostinger, UAB
Registrar IANA ID: 1636
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Registry Registrant ID: Not Available From Registry
Registrant Name: beatrice agatha agatha
Registrant Organization: beatrice agatha agatha
Registrant Street: RUDDY CREEK   
Registrant City: CHESTERFIELD
Registrant State/Province: VA
Registrant Postal Code: 23234
Registrant Country: US
Registrant Phone: +1.8042149939
Registrant Phone Ext:
Registrant Fax:
Registrant Fax Ext:
Registrant Email: beatriceagatha1@hotmail.com
Registry Admin ID: Not Available From Registry
Admin Name: beatrice agatha agatha
Admin Organization: beatrice agatha agatha
Admin Street: RUDDY CREEK  
Admin City: CHESTERFIELD
Admin State/Province: VA
Admin Postal Code: 23234
Admin Country: US
Admin Phone: +1.8042149939
Admin Phone Ext:
Admin Fax:
Admin Fax Ext:
Admin Email: beatriceagatha1@hotmail.com
Registry Tech ID: Not Available From Registry
Tech Name: beatrice agatha agatha
Tech Organization: beatrice agatha agatha
Tech Street: RUDDY CREEK  
Tech City: CHESTERFIELD
Tech State/Province: VA
Tech Postal Code: 23234
Tech Country: US
Tech Phone: +1.8042149939
Tech Phone Ext:
Tech Fax:
Tech Fax Ext:
Tech Email: beatriceagatha1@hotmail.com
Name Server: ns1.hostinger.com
Name Server: ns2.hostinger.com
Name Server: ns3.hostinger.com
Name Server: ns4.hostinger.com
DNSSEC: Unsigned
Registrar Abuse Contact Email: abuse@hostinger.com
Registrar Abuse Contact Phone: +37064503378
URL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/
```

The last thing I tried was querying `whois` for information on the domain name. All the information is of course, fraudulent (I believe the listed phone number and address belongs to [BAE Systems](https://www.baesystems.com/)). Still, it's not completely useless. We can do a [reverse whois lookup](http://viewdns.info/reversewhois/?q=beatriceagatha1%40hotmail.com) to find other domains registered with the same fake data:

```
Reverse Whois results for beatriceagatha1@hotmail.com
==============

There are 4 domains that matched this search query.
These are listed below:

Domain Name	Creation Date	Registrar
bn-ap.com   2018-05-16   HOSTINGER UAB
lt-pd.com   2018-05-16   HOSTINGER UAB
mq-tx.com   2018-05-16   HOSTINGER UAB
tx-app.com  2018-05-16   HOSTINGER UAB
```

Unfortunately, all of these have since been taken down.
