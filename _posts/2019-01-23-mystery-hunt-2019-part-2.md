---
layout: post
title:  "Mystery Hunt 2019 [Part 2/2]"
date:   2019-01-23 18:12:47 -0500
categories: mystery_hunt puzzlehunts
---

*This is the second of two posts about the 2019 MIT Mystery Hunt. You can see the hunt website with all the puzzles and solutions [here](http://web.mit.edu/puzzle/www/2019/full.html), and read [my first post]({{ site.baseurl }}{% post_url 2019-01-23-mystery-hunt-2019-part-1 %}) about the hunt theme and my experience.*

Here's the obligatory list of my favorite puzzles from this hunt:

<!--more-->

**[Send yourself Swanlumps](http://web.mit.edu/puzzle/www/2019/puzzle/send_yourself_swanlumps.html):** This was a physical puzzle that took the form of a choose-your-own adventure book parodying the [*Give yourself Goosebumps*](https://en.wikipedia.org/wiki/Give_Yourself_Goosebumps) series. The puzzle had at least three layers embedded within the book, the most interesting of which involved solving a bunch of aptly-named [Split Decisions](https://www.nytimes.com/2018/12/29/crosswords/variety-split-decisions.html) mini-puzzles. You can find a pdf of the book on the website, but I think it was a ton of fun to jump from page to page with the physical copy we were given.

**[Cross Campus](http://web.mit.edu/puzzle/www/2019/puzzle/cross_campus.html):** We're given a video consisting of various shots panning left-to-right or top-to-bottom of objects placed at various locations in MIT's campus. The key insight was realizing that this was actually a *crossword* puzzle presented as a video: the number of the MIT building where the object was located was the clue number, the name of the object was the answer to the clue, and left-to-right shots corresponded to across clues and top-to-bottom shots corresponded to down clues. Once you recreated the crossword, reading the diagonal yielded the answer.

**[I AM GROOT](http://web.mit.edu/puzzle/www/2019/puzzle/i_am_groot.html):** The puzzle is a really long page with "I AM GROOT" written at various locations and separated by 5 black bars. The 4th bar links to this image:

![i am groot]({{ site.baseurl }}/assets/puzzles2019/groot.jpg)

This is a shot from the fourth of [five post-credit scenes](https://www.youtube.com/watch?v=0o3xIQD7ATE&t=7s) that are interspersed at the end of the movie [*Guardians of the Galaxy Vol. 2*](https://en.wikipedia.org/wiki/Guardians_of_the_Galaxy_Vol._2). Perhaps now you can guess at the solution: stitch together the credits from the movie into one long image and precisely overlay it over the puzzle. The I AM GROOTs fall on top of words that appear in the credits. Diagonalizing the words yields the final answer.

**[Bloom Filter](http://web.mit.edu/puzzle/www/2019/puzzle/bloom_filter.html):** This puzzle, which of course appeared in Bloomsday Town, gave you a [Bloom Filter](https://en.wikipedia.org/wiki/Bloom_filter) implemented in client-side javascript which would either accept or reject any string of letters given to it. By running a large wordlist through the filter programmatically, we found the set of 68 unique words that it accepted. These words could be grouped into 17 sets of 4 words that were related to each other. For example, RATES, STARE, TASER, and TEARS was one set, consisting of anagrams of the letters A, E, R, S, T; and ALADDIN, GENIE, IAGO, JAFAR was another set consisting of characters from the movie *Aladdin*. Each set is "missing" one word which happens to be the name of a flower (a bloom!). For example, our anagrams set is missing ASTER, and our Aladdin set is missing JASMINE. Passing the missing words through the filter, and doing a some bit manipulation of the filter's internal state produces the answer.

**[Far Out](http://web.mit.edu/puzzle/www/2019/puzzle/far_out.html):** This is a pretty straightforward crossword puzzle distinguished only by its hilarious fill. For example, "What members of the cow-ngregation do after a prayer" is COWAMEN, and "Guy who’s only sort of fratty" is SEMIBRO. It turns out that this is a crossword variant called "[Something Different](https://devjoe.appspot.com/huntindex/keyword/somethingdifferentcrossword)", which given how amusing it is to solve, is tragically underused in puzzlehunts.

**[You're Gonna Need a Bigger Gravy Boat](http://web.mit.edu/puzzle/www/2019/puzzle/youre_gonna_need_a_bigger_gravy_boat.html):** A international maritime signal flags jigsaw puzzle that turns into a flag semaphore logic puzzle. Need I say more?

**[Delightful](http://web.mit.edu/puzzle/www/2019/puzzle/delightful.html):** The puzzle gives you a ridiculously funny nine-minute long video which demonstrates various made-up yoga poses each of which are claimed to be modifications of a real yoga asana. It turns out that each fake pose actually depicts the contortions of a person in the famous Hieronomys Bosch piece [*The Garden of Earthly Delights*](https://en.wikipedia.org/wiki/The_Garden_of_Earthly_Delights). Corresponding the transliterated Sanskrit name of the real Yoga pose and the position of the person on the Bosch painting allows you to extract the answer.

![the holiday forest]({{ site.baseurl }}/assets/puzzles2019/combined.png)

**[Sage Advice](http://web.mit.edu/puzzle/www/2019/puzzle/sage_advice.html):** There's a variety puzzle type developed by the great [Patrick Berry](http://aframegames.com/store/?page_id=140) called [Seven Sages](http://online.wsj.com/public/resources/documents/puzzle140816.pdf). By the time you finish this puzzle, you'll never want to look at a Seven Sages puzzle again. It gives you four seven sages puzzles each of which has a twist: in the first puzzle the grid is broken up into pieces that you have to reassemble, in the second, some cells in the grid must contain trigrams instead of single letters, and in the third, any consecutive appearance of the letters "sage" are removed from the clues. The fourth puzzle is standard, but you must use instructions you found in the earlier three to manipulate it to extract the answer. The whole thing is pleasingly nuts.

**[Running for Office](http://web.mit.edu/puzzle/www/2019/puzzle/running_for_office.html):** For whatever reason, I didn't do any runaround puzzles during this hunt, which was a shame because there were some good ones. When I woke up early Saturday morning and headed to our hunt base, someone encouraged me to look at this one, a runaround with some election campaigning flavor (it appeared in President's Day town). All the running around had been completed the night before and we were just stuck on extraction. A combination of my campus knowledge and my fresh eyes allowed us to break through: each location visited on the solution path involved a location named after a past MIT president. Sorting and indexing by the president's ordinal number led us to a final location in MIT.nano, where we found the answer.

Some honorable mentions: a [jigsaw crossword puzzle](http://web.mit.edu/puzzle/www/2019/puzzle/we_see_thee_rise.html) that is secretly in the shape of the Canadian flag, a [puzzle parody](http://web.mit.edu/puzzle/www/2019/puzzle/comma_and_a_freaking_dot.html) of The Lonely Island's song ["Semicolon"](https://www.youtube.com/watch?v=M94ii6MVilw), a [Bacon cipher](http://web.mit.edu/puzzle/www/2019/puzzle/helvetica_is_only_an_okay_font.html) that uses Helvetica and Arial as its two different typefaces, and a puzzle about [fake colors](http://web.mit.edu/puzzle/www/2019/puzzle/deep_blue.html) generated by a neural net.

Oh, and how could I forget what ended up being the hardest puzzle of the whole weekend: [escaping Boston](https://boston.cbslocal.com/2019/01/21/logan-flight-status-delays-cancellations-weather/) after a snowstorm that froze all but one of the runways at the airport. I ended up spending an extra 40 hours in Massachusetts (way too many of which were spent in Boston Logan's Terminal C), but I'm typing these words from California where the high today was a beautiful 61°F, so everything seems to have worked out okay.
