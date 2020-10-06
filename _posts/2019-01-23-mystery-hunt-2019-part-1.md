---
layout: post
title:  "Mystery Hunt 2019 [Part 1/2]"
date:   2019-01-22 17:12:47 -0500
categories: mystery_hunt puzzlehunts
tags: mystery_hunt puzzlehunts
---

*This is the first of two posts about the 2019 MIT Mystery Hunt. You can see the hunt website with all the puzzles and solutions [here](http://web.mit.edu/puzzle/www/2019/full.html), and read [my second post]({{ site.baseurl }}{% post_url 2019-01-23-mystery-hunt-2019-part-2 %}) about specific puzzles I enjoyed.*

This past Monday was Martin Luther King Jr. Day, which means that another [Mystery Hunt](http://web.mit.edu/puzzle/www/) has come and gone. For the second time, I hunted with teammate, which consists of a core group of MIT undergrads and recent alumni joined with several other puzzlehunting groups from CMU, Stanford, Berkeley, and a smattering of solvers from across the globe. The dominant feature of our team is definitely our age: almost all of us are in our early to mid-twenties. We had 90 people on our roster, which in practice meant something like 40 on-site solvers and around 10 active remote solvers.

<!--more-->

This year's theme was an homage to the film [*The Nightmare before Christmas*](https://en.wikipedia.org/wiki/The_Nightmare_Before_Christmas) and the [Great Molasses Flood](https://en.wikipedia.org/wiki/Great_Molasses_Flood), an event that took place exactly 100 years ago in which a storage tank burst and the molasses contained within it flooded the streets of Boston's North End neighborhood, killing 21 people. The opening skit involved Setec Astronomy (the writing team), creating the new holiday "Molasses Awareness Day," which immediately led to disaster: you see, in the movie, each celebrated holiday exists as a town in the "[Holiday Forest](http://web.mit.edu/puzzle/www/2019/full.html)", and the molasses from the newly created Molasses Awareness Day town, was spreading fast and causing problems for all the other towns in the forest.

These "problems" formed the [metapuzzles](https://en.wikipedia.org/wiki/Metapuzzle) and—for whatever reason—only occurred at the border between two adjacent towns. So, each puzzle was associated with a specific Holiday town, and metapuzzles would use some of the answers from one town and some of the answers from another. The holiday towns themselves started out relatively normal, with Christmas town, Halloween Town, and Valentine's Day Town, but the towns we unlocked later were delightfully more obscure (Pi Day town, Holi town, Arbor Day town). I think my favorite was probably [Bloomsday](https://en.wikipedia.org/wiki/Bloomsday) town, which allowed for a number of puzzles that were themed around James Joyce.

![the holiday forest]({{ site.baseurl }}/assets/puzzles2019/holidayforest.png){: .center-image }

The last hunt that Setec wrote was the [2017 hunt](http://web.mit.edu/puzzle/www/2017/index.html), which several teams completed before Saturday evening. In comparison, only one team (Left Out), ended up finishing this year's hunt, at 5:49 PM on Sunday, which meant that everyone got to experience a full 54 hours of puzzling.

This was due to a combination of several factors. First, while the number of puzzles and average difficulty were probably comparable to the 2017 hunt, Setec arranged them such that easier puzzles appeared earlier and harder puzzles appeared later, which forced you to solve several difficult puzzles in the later rounds in order to finish their metas. Also, the quirk with the metas described above made everything more devilish: in contrast to the supermetas in 2018 which involved intricate constructions, Setec seems to prefer metapuzzles that involve just one or two insights about the puzzle answers before yielding an answer. This led to pretty easy metas in 2017, but in this hunt, where you didn't know which answers actually belonged to the meta, it made for a pretty tricky challenge.

I spent the majority of the hunt working on metapuzzles, many of which took several hours of hard work to resolve. Our focus seems to have [paid off](http://web.mit.edu/puzzle/www/2019/statistics.html): teammate solved the third-most number of metas by the time the coin was found (well-behind Left Out and Palindrome, and slightly ahead of Death and Mayhem and our sister team ✈✈✈ Galactic Trendsetters ✈✈✈). Last year, we finished 12th, behind a number of talented teams. I'm still not exactly sure what accounts for our dramatic improvement given that our team size only increased modestly, but we're all pleased with our performance.

By the way, I referred to ✈✈✈ Galactic Trendsetters ✈✈✈ as our "sister" team above, but perhaps I should have said "twin" instead. During wrap-up, we were shown the following chart showing puzzles solved on the horizontal axis, and submission accuracy on the vertical axis. Circle size represents registered team size:

![the holiday forest]({{ site.baseurl }}/assets/puzzles2019/tfloorpite.png){: .center-image }

We had a hard time finding teammate at first, until we realized that our circle was almost perfectly positioned behind `floorpi` (the username of ✈✈✈ Galactic Trendsetters ✈✈✈). Later, we were doing a little science on the stats Setec posted and noticed the following:

```
      Response  | teammate | floorpi
----------------+----------+----------
       UNLOCKED |    192   |   192
      INCORRECT |    150   |   151
        CORRECT |    169   |   169
         SOLVED |    158   |   157
SOLVENT APPLIED |     22   |   22
```

<br>

More spooky stats:
 * teammate and ✈✈✈ Galactic Trendsetters ✈✈✈ have the second and first fastest solve times for any puzzle in the hunt (both were < 1 minute backsolves of [Turtle Power!](http://web.mit.edu/puzzle/www/2019/puzzle/turtle_power.html))
 * teammate and ✈✈✈ Galactic Trendsetters ✈✈✈ unlocked [Movie Marathon](http://web.mit.edu/puzzle/www/2019/puzzle/movie_marathon.html), the ~~weeb~~ anime puzzle at the same time, and solved it within 2 minutes of each other. The puzzle consisted of a 45 minute long video, and both teams solved it in less than 1 hour and 15 minutes. (Also, the puzzle was unlocked at 4 in the morning).
 * You'll notice that in the chart above the `CORRECT` count is greater than the `SOLVE` count. This is because sometimes multiple people will submit the same correct answer simultaneously, both of which are marked correct (although the puzzle itself is "solved" only once of course). Amazingly, this happened pretty much the exact same number of times for each of the two teams!

<br>
In my [next post]({{ site.baseurl }}{% post_url 2019-01-23-mystery-hunt-2019-part-2 %}), I talk about specific puzzles that I enjoyed solving / watching get solved.
