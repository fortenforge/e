---
layout: default
title: Puzzlehunts
permalink: /puzzlehunts
---

*What's a puzzlehunt? Read [this](https://blog.vero.site/post/puzzlehunts) first.*

# Resources

* A [guide](https://docs.google.com/spreadsheets/d/1jMFf_fSEH9kL9_PAYyMxjL8pRZdgqCm9n2xrnF69kQM/edit?usp=sharing) to puzzlehunting using Google Sheets
* A [glossary](https://docs.google.com/document/d/1_d_xx-RQ2mCfXGVtjCx_56pQhwdJHvyBser7pHLtmM0/edit#) of puzzlehunting terms
* A [guide](https://github.com/fortenforge/suggestions-for-running-a-puzzlehunt) to running a puzzlehunt (feel free to submit a pull request!)
* A [map](https://fortenf.org/mh-coin-map) of coin locations for the MIT Mystery Hunt
* Some [tools](https://fortenf.org/codes) for quickly decoding Morse code, Braille, and flag semaphore
* A [tool](https://fortenf.org/caesar) for quickly looking at Caesar shifts

# Puzzles I've written / co-written

### 2021 MIT Mystery Hunt

* [★](https://perpendicular.institute/puzzle/%E2%98%85/)
* [Alternate Controls](https://perpendicular.institute/puzzle/alternate-controls/)
* [Look at What We Drew](https://perpendicular.institute/puzzle/look-at-what-we-drew/)
* [Magic Words](https://perpendicular.institute/puzzle/magic-words/)
* [Thank You to Our Sponsors](https://perpendicular.institute/puzzle/thank-you-to-our-sponsors/)
* [Maseeh Hall](https://perpendicular.institute/puzzle/maseeh-hall/)
* [Burton-Conner](https://perpendicular.institute/puzzle/burton-conner/)
* [MacGregor House](https://perpendicular.institute/puzzle/macgregor-house/)
* [Dorm Row](https://perpendicular.institute/puzzle/dorm-row/)
* [Simmons Hall](https://perpendicular.institute/puzzle/simmons-hall/)
* [Student Center](https://perpendicular.institute/puzzle/student-center/)
* [Football](https://perpendicular.institute/puzzle/football/)
* [Rule of Three](https://perpendicular.institute/puzzle/rule-of-three/)
* [Twins](https://perpendicular.institute/puzzle/twins/)
* [Level One](https://perpendicular.institute/puzzle/level-one/)
* [⊥IW.nano](https://perpendicular.institute/puzzle/%E2%8A%A5IW.nano/)
* [Nutraumatic](https://perpendicular.institute/puzzle/nutraumatic/)
* [Telephone](https://perpendicular.institute/puzzle/telephone/)
* [Extensive](https://perpendicular.institute/puzzle/extensive/)
* [Don't Get Eaten!](https://perpendicular.institute/puzzle/dont-get-eaten/)
* [Bingo](https://perpendicular.institute/puzzle/bingo/)
* [Endgame](https://perpendicular.institute/endgame)

### 2019 Galactic Puzzle Hunt

* [Ten Years Later](https://2019.galacticpuzzlehunt.com/puzzle/ten-years-later)
* [The Animal Trainers](https://2019.galacticpuzzlehunt.com/puzzle/the-animal-trainers)
* [The Galactic Conference](https://2019.galacticpuzzlehunt.com/puzzle/the-galactic-conference)

### 2018 Galactic Puzzle Hunt

* [Tabloid News](https://2018.galacticpuzzlehunt.com/puzzle/tabloid-news)
* [Consolation Prize](https://2018.galacticpuzzlehunt.com/puzzle/consolation-prize)
* [Chessmen Meta](https://2018.galacticpuzzlehunt.com/puzzle/chessmen)
* [Animal Crackers Meta](https://2018.galacticpuzzlehunt.com/puzzle/animal-crackers)

### 2017 Galactic Puzzle Hunt

* [Puncturing Sensation](https://2017.galacticpuzzlehunt.com/puzzle/3/1.html)
* [Duck Quonundrum](https://2017.galacticpuzzlehunt.com/puzzle/6/4.html)

### 2013 MIT Mystery Hunt

* [lol](http://www.mit.edu/~puzzle/2013/coinheist.com/feynman/lol/)
* [Linked Pairs](http://www.mit.edu/~puzzle/2013/coinheist.com/sneakers/linked_pairs/)



<div class="home">

  <h1 class="page-heading">Puzzlehunt Writeups</h1>

  <ul class="post-list">
    {% for post in site.tags.puzzlehunts %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>: <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
      </li>
    {% endfor %}
  </ul>

</div>
