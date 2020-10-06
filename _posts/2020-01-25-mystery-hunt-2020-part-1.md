---
layout: post
title:  "Mystery Hunt 2020 [Part 1/3]"
date:   2020-01-25 11:12:47 -0500
categories: mystery_hunt puzzlehunts
tags: mystery_hunt puzzlehunts
---

*This is the first of three posts about the 2020 MIT Mystery Hunt. You can see the hunt website with all the puzzles and solutions [here](http://pennypark.fun).*

I spent the past weekend in Cambridge, MA participating in the [MIT Mystery Hunt](http://web.mit.edu/puzzle/www/) for the sixth time. I was the captain of **teammate**, a rather new team that split from ✈️✈️✈️Galactic Trendsetters✈️✈️✈️ (the eventual winners of this year's hunt) in 2018, and whose increasingly strong performance has taken many by surprise.

<!--more-->

teammate spent much of the hunt running neck-and-neck with other teams and actually leading the pack for most of Saturday. Eventually however, Galactic pulled ahead and beat us to the final meta solve by around three hours. (After that, both of us got stuck on the final "capstone" puzzle for far too long, but maybe I'll talk more about that in a later post.) You can see a graph of puzzles solved over time below. teammate's line is the green one at the top.

[![progress chart]({{ site.baseurl }}/assets/puzzles2020/progress.png){: .center-image }]({{ site.baseurl }}/assets/puzzles2020/progress.png)

Even better than this line plot though, is this visualization created by Wei-Hwa Huang on Left Out (the writing team):

<div class="flourish-embed flourish-bar-chart-race" data-src="visualisation/1267425"></div><script src="https://public.flourish.studio/resources/embed.js"></script>

As I described in [last year's post](https://fortenf.org/e/mystery_hunt/puzzlehunts/2019/01/22/mystery-hunt-2019-part-1.html), teammate is more or less a fusion of multiple puzzlehunting groups from MIT, CMU, and Berkeley. Some of us—including myself—write for the [Galactic Puzzle Hunt](https://galacticpuzzlehunt.com/), and will be joining the trendsetters to help them write the 2020 hunt. Others, inspired by how well we did this year, intend to participate in Mystery Hunt again and hopefully win outright someday.

Here's how my hunt experience went down: on Wednesday evening I took a red-eye flight to Boston from SFO and landed early Thursday morning. Last year I took a red-eye leaving on Thursday and ended up starting hunt on Friday already sleep-deprived, which I discovered was a really bad idea. This year I was much better rested.

As is now tradition, a large fraction of teammate met at Flour on the morning of hunt on Friday and had a quick brunch before walking over to Kresge for kickoff. This year's kickoff will forever be remembered as the one that included [*a literal wedding*](https://www.youtube.com/watch?v=mOKqITBPo2w) between two long-time hunt participants. The wedding itself was not really part of the hunt theme, but Left Out tied it in by explaining that for the newlywed's honeymoon, teams would get a chance to explore *Penny Park*, a fictional amusement park that had fallen on some tough times—the real theme of the 2020 Mystery Hunt.

My first impression of the theme was some skepticism over whether a theme park was sufficiently exciting to make for an interesting hunt, but Left Out proved me wrong. *Penny Park* included ten different lands (rounds of puzzles) that each fit right at home in a theme park, but also felt thematically distinct and a rich source for novel round structures. For example, one of the lands was a water park called [Cascade Bay](http://pennypark.fun/land/cascade/), which included three "levels", each of which had a meta that used all the answers from puzzles in the current AND prior levels (meant to evoke the idea of water cascading down through the park).

So yeah, the theming really impressed me this year and I think all-told this hunt makes it into my top 3 favorites (perhaps behind 2015 and 2018). Anyways, back to my hunt experience: immediately after kickoff ended, some teammates scanned through the hunt rules and realized that this year's hunt would feature automated answer checking (what a novelty!). In past mystery hunts, you could submit your guesses through the hunt website, but someone from the running team would call you back to tell you if your answer was correct; this year you'd know immediately.

A few of us quickly realized that an implication of this change was that we could submit large numbers of guesses and backsolve attempts without the organizers getting mad at us, and during the minutes before the hunt started I encouraged everyone to submit a guess as long as they were >10% sure that it could be the right answer. (By the way, it turns out that 10% was an optimal choice given the [precise details](https://puzzlvaria.wordpress.com/2020/01/20/2020-mit-mystery-hunt-part-1-coping-with-anticlimax/#comment-831) of the rate-limiter, but we didn't know that at the time).

I realize that this aggressive guessing strategy seems horrifying to some teams, but for better or worse (and in this hunt I think definitely for better), it's how teammate approaches puzzlehunts. It's a practice that we inherited at least partially from Galactic, and it may have played a small part in our success this year. Below is a graph of the number of guesses vs. the number of solves for all the teams:

[![guess stats]({{ site.baseurl }}/assets/puzzles2020/guess_stats.png){: .center-image }]({{ site.baseurl }}/assets/puzzles2020/guess_stats.png)

teammate's dot is the one in the top right just to the left of the dot labeled trendsetters. We were all amused to discover that both teammate and Galactic made exactly 872 guesses (over 200 more guesses than the next largest team):

![thonk]({{ site.baseurl }}/assets/puzzles2020/ten_percent.png){: .center-image }

Hunt started at 1:30pm on Friday without a hitch, and teammate started racking up solves. Like most competitive teams, teammate collaboratively solves puzzles on spreadsheets and uses a master spreadsheet to keep track of all the unlocked puzzles and answers. We also have a script that scrapes the website for new puzzles, creates new spreadsheets and rows on the master sheet, and sends alerts to our Discord server. This is what our master sheet looked like at the end of this year's hunt (I've cleared the answer column to avoid spoilers):

[![to me this is beautiful]({{ site.baseurl }}/assets/puzzles2020/master.png){: .center-image }]({{ site.baseurl }}/assets/puzzles2020/master.png)

It's incredibly satisfying to watch the sheet get longer and longer and to watch the green line of solved puzzles in column C slowly but surely march downwards.

Most of hunt was pretty uneventful. Of course, we solved a ton of really cool puzzles (I'll talk about some of my favorites in an upcoming post), but our intuitions about how the hunt would unfold proved true for the most part. The first truly exciting development happened around 4am on Sunday when we got a call from Left Out warning us that we were in contention to win and wanting verbal confirmation that if we did win we were ready and willing to write next year's hunt.

Most of teammate has very little puzzle-writing experience, and I'll confess that the prospect of leading a teammate writing team rather terrified me. At the same time, it *really* didn't seem like I could ask my amped-up team to slow down on puzzle solving just because I was scared, so I confirmed with Left Out that we were prepared to write if it came to that. Luckily, we did have an agreement among the Galactic Puzzle Hunt authors that most of us would be willing to join and help out teammate or Galactic if either team won hunt, so I think we'd have managed.

Every time we solved a round-meta in the hunt, we were asked to send someone to the "Workshop" to pick-up one or more custom-pressed pennies. Whoever was sent actually got to press the pennies themselves (with Left Out's assistance) and I found the process pretty cool—Left Out even had some vinegar and water to polish the pennies before they were pressed. We'd been mostly ignoring the pressed pennies, assuming that they corresponded to a final "post-meta" that we hadn't unlocked yet. At 8:45 AM we solved what we thought was our final meta, and as expected, we unlocked the post-meta once we collected the final two pennies.

[![smh these pennies]({{ site.baseurl }}/assets/puzzles2020/penny.jpg){: .center-image }]({{ site.baseurl }}/assets/puzzles2020/penny.jpg)

In our excitement, we decided that we should move our entire team from our the dorm we were hunting out of to MIT's main campus where we could quickly solve the post-meta and then go on the runaround. We all gathered on the fourth floor of Lobby 7 and started staring at the pennies. We made some good progress in the first hour, but after that we got completely stuck. Most of us had pulled an all-nighter the prior night and ended up tired and frustrated that we couldn't solve the one thing in between us and the coin.

We eventually returned the pennies and ourselves to our dorm, and at this point I decided that since I wasn't contributing anything useful to the puzzle, I decided to go back to sleep, asking to be roused if any major developments occurred. Around three hours later I got a frantic phone call from a teammate: "GALACTIC FOUND THE COIN AND LEFT OUT CALLED US AND LET US SKIP THE PENNIES PUZZLE AND WE'RE GOING ON THE RUNAROUND RIGHT NOW WHERE ARE YOU!?". (At least that's what it sounded like to my very tired brain).

So then of course I got up and joined my teammates for the runaround (which ended up being ten runarounds in parallel, but more on that later I guess) and the endgame, which was quite nice. After that, I grabbed a quick dinner, had a meeting with Galactic regarding what to do about Galactic Puzzle Hunt 2020 (we'll have an announcement soon…), and then slept for 11 hours nearly missing [wrap-up](https://www.youtube.com/watch?v=WjM0mSgoqPI). By the time I arrived, the room had already reached capacity, but luckily some had teammates saved me a seat and I knew an alternate entrance to get past the folks turning people away.

After wrap-up, I spent the rest of my time in Cambridge frantically walking and ubering from a lunch in Harvard Square to an escape room in Central Square to a meeting with Galactic and Left Out at MIT and finally to the airport. All in all, it was a wonderful—if slightly more hectic than planned—weekend, and I can't wait to be back to help *run* hunt for the first time next year!
