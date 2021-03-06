---
layout: post
title:  "No, you couldn't have made more money than the Twitter hacker"
summary: "Analysis of various approaches to monetizing the Twitter hack"
image: assets/twitter_hack/stonks.png
date:   2020-07-15 18:12:47 -0500
categories: security
tags: security
---

<style>
  .center {
    margin-left: auto;
    margin-right: auto;
  }
</style>

Picture this: you've stumbled across the vulnerability of the year. You figured out how to gain access to any verified Twitter user's account: Joe Biden, Kim Kardashian, Kanye West, XXXTentacion, Apple, you name it. That was step 1 of your evil plan. What should step 2 be?

Maybe you log into the president's account and post a fake tweet about launching missiles to North Korea. I'm not convinced that you can start a world war just by sending out a few fake tweets, but for the sake of argument let's say that you can. But you're not interested in watching the world burn anyway; no, your goal is to **make as much money as possible**. What should you do?

The people who carried out today's Twitter hack had one answer: post variants of the message "send 1 BTC to this address and I'll send 2 BTC back"  on famous accounts, and then (here's the kicker) *don't actually send 2 BTC back*.

![image](https://video-images.vice.com/test-uploads/_uncategorized/1594848885006-image.png){: .center-image }

They managed to run off with a little over [$100,000](https://www.blockchain.com/btc/address/bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh?page=2) before Twitter got the situation under control. Several people (on Twitter, on Hacker News, everywhere really) scoffed at this and claimed that they would have done something much more interesting with this kind of power. Let's explore some of their ideas to see if we really can do better.

<!--more-->

## Stock Market Manipulation

This is the first thing that comes to most people's minds. It sounds easy: buy a bunch of Tesla stock, log into Elon Musk's account, tweet "Taking $TSLA private at $2000," and sell in the time between the rise in price and when Musk/Twitter/the whole world realizes the hack.

This requires a fair amount of capital to buy the underlying stock, which the hacker may not have had, but there are other options: maybe you short the stock and then tweet (fake) damaging information about Tesla's new line of cars. Maybe you just short Twitter's own stock, which will surely take a huge hit in the market tomorrow (the hack was executed after trading hours).

<blockquote class="twitter-tweet tw-align-center"><p lang="en" dir="ltr">oh lol i&#39;d buy HTZ, that&#39;s the trade. &quot;i&#39;ve decided that Tesla will buy Hertz out of bankruptcy at $69 per share and make its fleet entirely self-driving,&quot; i&#39;d be a billionaire and Money Stuff would end on the highest possible note</p>&mdash; Matt Levine (@matt_levine) <a href="https://twitter.com/matt_levine/status/1283503080978489344?ref_src=twsrc%5Etfw">July 15, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The big issue with all of these is that it's very difficult to participate in the stock market anonymously. The SEC has all sorts of monitoring in place to catch more common forms of insider trading and fraud and you can guarantee that they would conduct a long, thorough investigation into a hypothetical hack-based market fraud. Unlike Bitcoin transactions, wire transfers and stock purchases can be reversed after the fact, and the exposure and risk go way up when you're actually working with US dollars.

## Blackmail

The hackers got *full access* to all Twitter-verified accounts, meaning that they should have had access to their direct messages. Surely this is much more valuable than just posting a fake tweet? Think about the incalculable damage caused by the [Sony leaks](https://www.vanityfair.com/news/2019/10/the-untold-story-of-the-sony-hack) or watch this hypothetical-but-still-terrifying [Tom Scott video](https://www.youtube.com/watch?v=y4GB_NDU43Q) about a world in which Gmail password-checking was turned off for a day.

It's definitely true that there are gobs of interesting material lurking in verified users's DMs that they wouldn't want seeing the light of day, but it's a little tricky to make money from this. The most obvious method is blackmail. Exfiltrate the messages from the most popular users and threaten that you'll release them in full if they don't send X BTC to Y address.

This would definitely make some amount of money, but I'm not convinced it's worth the effort. Firstly, it's hard to judge ahead of time which accounts have the juicy DMs. Large, high-profile accounts like Joe Biden's are run by a whole social media team and probably don't exchange sensitive information via Twitter. Smaller accounts probably do have more interesting gossip, but might be less willing or able to pay as a result.

Then there's the problem of actually exfiltrating and storing all the data, sending individual messages to each of the users you hack, tracking who has and hasn't paid, and actually releasing the leaked material publicly. It's a lot of operational overhead that may, in the end, net you less money than the dumb Bitcoin scam.

## Sell it on the dark web

Leave aside the challenge of actually finding a buyer (you can't just make a post on a forum, the Feds read the dark web too). How much should a buyer be willing to pay you for this bug? Either the buyer is planning to monetize the exploit for more than $100k (the whole point of this post is to prove that that's really hard!), or the buyer wants to use the exploit for other purposes, like surveillance or intelligence gathering.

This isn't out of the question. Countries spy on each other all the time, and I'm sure that China would love to be able to read the DMs of journalists or Hong Kong activists. The issue is that this particular exploit wasn't terribly suited to the business of long-term surveillance. Logging into the account required actually the email associated with it and triggering a password reset (that somehow also bypassed 2FA). The password reset triggered a notification email to the original address on file, which basically announced the account takeover. The hacker had a matter of hours before the whole world knew about the vulnerability.

Intelligence agencies want *persistent* access, and they want to surveil without their targets learning that they were surveilled. This exploit wouldn't have accomplished that.

## Bug Bounty

Now we're getting somewhere... Large companies [routinely](https://www.forbes.com/sites/johnkoetsier/2020/05/31/hacker-finds-huge-apple-security-hole-apple-pays-100000-bug-bounty/#576388b36894) pay out vast sums of money to white hat hackers reporting flaws that are less damaging than this one. Even if Twitter wouldn't pay out 100% as much money as was made via the Bitcoin scam, bug bounty programs have the added benefit of being 100% legal, which should change the risk calculus significantly. So why didn't our hacker take this route?

One theory is that if the hacker was already a known criminal, it's tricky to collect the bug bounty payout without revealing their identity to Twitter, which might still end them up in some trouble. I'm not sure I buy this (can't they just use a third party?).

More plausibly, perhaps Twitter wouldn't actually have paid out $100k?

<blockquote class="twitter-tweet tw-align-center"><p lang="und" dir="ltr">🤔 <a href="https://t.co/I902KoaZwe">pic.twitter.com/I902KoaZwe</a></p>&mdash; LiveOverflow 🔴 (@LiveOverflow) <a href="https://twitter.com/LiveOverflow/status/1283511782380908545?ref_src=twsrc%5Etfw">July 15, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

This tweet suggests that OAuth takeovers were valued at $7.7k (and after taxes you're left with chump change). If so, this a big issue with Twitter's bug bounty program! You need to ensure that the incentives make reporting more worth it than exploiting.

*Update: more reporting about how the account takeovers were executed [has emerged](https://www.vice.com/en_us/article/jgxd3d/twitter-insider-access-panel-account-hacks-biden-uber-bezos), and it suggests that there wasn't any technical vulnerability that was ever exploited. Social engineering and insider risk (which can obviously be quite damaging) tend not to be eligible for bounties on most programs.*

## Phishing

OK how about this: log into Coinbase's twitter account, craft a tweet announcing some snazzy new feature and include a bit.ly link that actually goes to a phishing website. Execute a [relay phishing attack](https://fortenf.org/e/2018/05/29/coinbase-phishing.html) that gives you access to their Coinbase account while bypassing 2FA, and then send all their Bitcoin and Ethereum and Dogecoin to your own account.

There are a few flaws, but most are surmountable with some effort. First, Twitter would probably take down the tweet pretty fast, so you'd probably want to target several different accounts of famous cryptocurrency enthusiasts (not just Coinbase). Second, bit.ly would probably disable that specific URL and Google would definitely add the phishing site to their [Safe Browsing](https://safebrowsing.google.com/) list (which is shared by all major browsers). This would result in big scary red warning banners appearing before users could access your site. Also, whatever registrar you purchased your domain from would probably revoke it.

OK, so instead of having one phishing site and one domain and one URL you'd want to set up several of these in advance and hop from one to the other as each gets taken down by various actors in turn. This would probably work! Would it net you more than $100k? Maybe?! It's hard to say and probably depends a lot on luck and timing. The people with the most cryptocurrency in their Coinbase accounts are probably the least likely to fall for this kind of thing in the first place, so it's unclear how much more money you'd make than the dumb 2-for-1 scam, but still, it might be worth it in case you manage to compromise an account with huge reserves.

Still, all this requires some legitimately difficult software engineering work and the whole point of this was to make easy money! I understand that today's attack was almost painfully lame in its execution, but it really may have been the smartest choice tactically.

This is not to say that they did everything right: I still can't explain why they seem to have used only 1 BTC address throughout the process (Coinbase and other exchanges quickly blocked transactions to this address), and the wording on some of their tweets was stilted and awkward (surely they could have paid for a Grammarly subscription). But in the end, I think there's no evidence that the twitter posts were just a "cover" for the real attack or that this hack had anything to do with a foreign government. I think this tweet sums it up well:

<blockquote class="twitter-tweet tw-align-center"><p lang="en" dir="ltr">High impact bugs can sometimes be found and exploited by anyone, even dogs who have no idea what to do with the car they caught. There really doesn&#39;t have to be more to it than that.</p>&mdash; Brad Hill (@hillbrad) <a href="https://twitter.com/hillbrad/status/1283597241685438464?ref_src=twsrc%5Etfw">July 16, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

*Thanks to Akshay, Kim, and Varun for reading drafts of this.*
