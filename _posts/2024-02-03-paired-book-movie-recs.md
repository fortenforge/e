---
layout: post
title:  "2023 Paired Book/Movie Recommendations"
date:   2024-02-03 11:12:47 -0500
tags: recommendations
summary: "The best books I read and movies I watched in 2023—paired."
---

I wanted to write down some thoughts on the best books I read and movies I watched in 2023, but to make the exercise a little more interesting I decided to pair each book with a movie and talk a little bit about the connections between them.

Full disclosure—not all of these came out in 2023, but I did read/watch each one in the past year.

# *Oppenheimer* / *The Making of the Atomic Bomb*

<div id="comparison">
  <figure id="movie_img">
    <div id="book_img"></div>
  </figure>
</div>

<style>
div#comparison {
  width: 20vw;
  height: 30vw;
  max-width: 300px;
  max-height: 450px;
  overflow: hidden;
  position: fixed;
  left: 30px;
  bottom: 30px;
}

div#comparison figure {
  background-size: cover;
  position: relative;
  font-size: 0;
  width: 100%;
  height: 100%;
  margin: 0;
}

div#comparison figure>img {
  position: relative;
  width: 100%;
}

div#comparison figure div {
  background-size: cover;
  position: absolute;
  width: 50%;
  box-shadow: 0 5px 10px -2px rgba(0, 0, 0, 0.3);
  overflow: hidden;
  bottom: 0;
  height: 100%;
}
</style>

<script>
var divisor = document.getElementById("book_img");

function getScrollPercent() {
    var h = document.documentElement,
        b = document.body,
        st = 'scrollTop',
        sh = 'scrollHeight';
    return (h[st]||b[st]) / ((h[sh]||b[sh]) - h.clientHeight) * 100;
}

addEventListener("scroll", moveDivisor);

function moveDivisor(e) {
    let pct = getScrollPercent();
    console.log(pct);
    if (6 <= pct && pct < 30) {
        document.getElementById("book_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/the_making_of_the_atomic_bomb.jpg)";
        document.getElementById("movie_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/oppenheimer.jpeg)";
        divisor.style.width = (pct - 6.0)/24.0 * 100.0 + "%";
    } else if (30 <= pct && pct < 67) {
        document.getElementById("book_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/how_i_won_a_nobel_prize.jpg)";
        document.getElementById("movie_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/tar.jpeg)";
        divisor.style.width = (pct - 39.0)/37.0 * 100.0 + "%";
    } else if (67 <= pct && pct < 90) {
        document.getElementById("book_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/battle_royale.jpg)";
        document.getElementById("movie_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/godzilla_minus_one.jpeg)";
        divisor.style.width = (pct - 67.0)/23.0 * 100.0 + "%";
    } else if (pct >= 90) {
        document.getElementById("book_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/less.png)";
        document.getElementById("movie_img").style.backgroundImage = "url({{site.baseurl}}/assets/book_movie_pairs_2024/american_fiction.png)";
        divisor.style.width = (pct - 90.0)/10.0 * 100.0 + "%";
    } else {
        document.getElementById("book_img").style.backgroundImage = "";
        document.getElementById("movie_img").style.backgroundImage = "";
    }
}
</script>

I read Richard Rhodes' *The Making of the Atomic Bomb* in preparation for *Oppenheimer*'s release, and it is perhaps the best work of non-fiction I've ever read. Part scientific explainer, part historical / biographical narrative, the book charts the progress of physics from the turn of the 20th century all the way to the culmination of the Manhattan project. At the time of its release it was universally celebrated—it won the National Book Award and the Pulitzer Prize for nonfiction; the inside cover contains testimonials from five different Nobel laureates. At no point does Rhodes elect to simplify or hand-wave through the science; it's the most technically-demanding book intended for public consumption I've ever encountered.

The first third of the text is about nascent discoveries in a new field of science—nuclear physics. Rhodes outlines the key experiments: Ernest Rutherford's discovery of beta radiation, his gold foil experiment disproving the plum-pudding model of the atom, J.J. Thompson's discovery of the electron, James Chadwick's discovery of the neutron, and Enrico Fermi's neutron bombardment experiments leading to the discovery of new elements. All the while, conflict is brewing on the European continent. Germany's increasing militarism triggers a mass exodus of scientists to Britain and the US.

Fermi emigrates to Chicago to flee Fascist Italy; the British send a fighter bomber to evacuate Niels Bohr when the Nazis invade Copenhagen. European scientists who were previously part of a unified community suddenly found themselves cleaved in two. Prior to the schism, science had progressed far enough to suggest a frightening possibility: trapped within the nucleus of an atom was enough energy to change the course of the war.

The rest of the text is about the Manhattan project—convincing Roosevelt to launch the initiative, early fission experiments at the University of Chicago, the launching of the project, and the test at Trinity.

Going into *Oppenheimer*, I was excited to see all this depicted on-screen. I wanted to watch Meitner and Frisch ski while conceiving fission, I want to witness Fermi and Szilard build Chicago Pile-1, I want to see Lawrence build his calutrons, Seaborg synthesize plutonium, Von Neumann model the explosive lenses.

But Christopher Nolan was right not to indulge us: the genius of Rhodes' book was in its demanding technical sophistication; it's a poor fit for a 3-hour blockbuster aimed at a general public that barely knows what a neutron is.

Instead what we got was an almost straightforward biopic of Oppenheimer, a film more concerned with politics, philosophy, morality. It's the best thing Nolan has ever made—which is an absolutely bonkers thing to say given his filmography, but it's true.

More than anything, the screenplay nails the paradoxical nature of Oppenheimer: a brilliant theoretician who pulled off a triumph of experimentalism/engineering, someone intrigued by, but never wholly committed to left-wing politics, someone equally interested in quantum mechanics and Hindu classical texts.

Most importantly: someone who unflinchingly championed the development of the atomic bomb but immediately pivoted to "arms control" and moral quandary once he was no longer in charge. In Von Neumann's words: "sometimes someone confesses a sin in order to take credit for it". Indeed, history I think has proven Teller and Strauss right—the H-bomb—whose development Oppenheimer loudly opposed in public—was feasible and needed to be pursued to keep pace with the Soviet Union.

Further recommendations:
* If you want to learn more about the science behind the bomb, check out [Welch Lab's videos](https://www.youtube.com/watch?v=y-FuqptTZow&list=PLiaHhY2iBX9gPWroqwWRdLiWlWRZ1TE0L)
* If you enjoyed the black-and-white sections of *Oppenheimer* I suspect you'll love anything by Aaron Sorkin. In particular I recommend *The Trial of the Chicago 7*
* Rhodes also wrote a follow-up book, *Dark Sun*, about the making of the hydrogen bomb.

# *Tár* / *How I Won a Nobel Prize*

It's taken a surprisingly long time for the literary world and Hollywood to grapple with and respond to phenomena like the Me-Too movement and cancel culture—but in recent years artists finally seem brave enough to produce work that tackles these subjects (beyond of course, purely narrative works like [*She Said*](https://en.wikipedia.org/wiki/She_Said_(film))).

Chief amongst these is *Tár*, a remarkable film by director Todd Field about Lydia Tár, the conductor of a prestigious Berlin orchestra facing allegations of workplace misconduct. We watch Tár—brilliantly played by Cate Blanchett—argue with woke students at Juilliard, bully the classmate of her adopted daughter, fake a violent attack to cover up a fall, steal a pen off the desk of a colleague, and finally be chased out of the country to Asia, where she conducts the score for the *Monster Hunter* video game in front of an audience of cosplayers—demoted to a position she would have held in contempt just a year earlier, but still conducting, and still in love with music.

<iframe width="560" height="315" src="https://www.youtube.com/embed/uLVCs4G31jk?si=SsqtfrDgvNBaB7HN" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

*Tár* is endlessly thought-provoking: why did Field make his sexual harasser a woman? (why did he make her a lesbian?) What does Lydia Tár *deserve*? Can we separate the [Tár from the Tártist](https://letterboxd.com/grifflightning/film/tar-2022/)?

It is galling that Tár won nothing in last year's Academy Awards—losing in all of its most deserving categories to *Everything Everywhere All at Once*—a film whose most profound insight seems to be "yes, the multiverse renders life meaningless, but the existence of love and kindness still makes life worth living." Unlike *EEAAO*, *Tár* doesn't pretend to have all the answers—it wants to start a conversation, an argument, between its viewers; in contrast *EEAAO* is content with tiding us over with sophomoric humor and nihilism 101.

*How I Won a Nobel Prize*—the debut novel of Julius Taranto—is also a meditation on cancel culture. The premise is this: a wealthy billionaire establishes The Rubin Institute on an island off the coast of Connecticut. The Institute is explicitly a haven for the cancelled—any sufficiently renowned academic fired for saying the n-word in class or sleeping with a graduate student is invited to the Institute to conduct their work undisturbed by the reprobations of society.

Helen, our novel's protagonist, is a brilliant graduate student studying high-temperature superconductivity. When her advisor is pushed out of his current post and elects to join the Institute, Helen decides to follow him—bringing her reluctant partner Hew in tow. The endless arguments between Helen and Hew about the morality of the Institute and its surrounding culture wars form the heart of the novel. They're a chance for Taranto to argue with himself—and to invite the reader to ponder their own stance. Is it worth tolerating intolerance if it gives you the opportunity to make practical superconductivity (and make practical all the things that come with that—limitless clean energy, levitating trains, ultra fast computing speeds)? What are the appropriate consequences for violating a societal norm—total expulsion, social shunning?

Through Helen and Hew's eyes we get to see the Institute at work—its cafeteria serves "ostentatiously problematic meat" ("foie gras, roast suckling pig, octopus, horse"), undergraduates are educated in the Canon—whose syllabus includes only dead, white writers, and the Institute's only building is a phallus-shaped tower positioned so that its upper residents can look down at Yale (the top floor contains a telescope meant for exactly this purpose).

While the ending of the novel—which I will not spoil—pales in comparison to *Tár*'s sublime ending, on the whole it is a worthy read. Taranto—who has no scientific background whatsoever—nails the technical jargon around the superconductivity discussions (at least it was good enough to not stick out to me). Like Tár, the novel doesn't pretend to have all the answers to the thorny questions it provokes, but you'll come out of it with a better appreciation for the perspectives of each side.

Further recommendations:
* I loved [this review](https://www.nybooks.com/articles/2023/01/19/the-instrumentalist-tar-todd-field-zadie-smith/) of Tár by Zadie Smith that appeared in the *New York Review of Books* (paywalled, but you can create a free account just to read this one piece.)
* Listen to [the rest](https://open.spotify.com/album/2mxjaerL9JdNbLnGdCfh8Q?si=0LtkF9C_TgGt9pyU8eFPqQ) of Mahler 5, the piece that Lydia Tár is working on throughout the film
* Read [an explainer](https://www.nytimes.com/2023/08/03/science/lk-99-superconductor-ambient.html) about LK-99 (the "superconductor of the summer") and superconductivity in the New York Times

# *Godzilla Minus One* / *Battle Royale*

The thing that everyone seems to say about *Godzilla Minus One*—Japan's newest Godzilla movie—is how good it looks despite having a budget of only $15 million. This is certainly true—the film rivals anything that Hollywood has produced in the past few years in terms of visual effects and was just nominated for a VFX Oscar, but that's not the most interesting thing about it.

The thing that *Godzilla Minus One* gets that the American *Godzilla* films do not is that it needs to be about more than just kaiju attacks. The original *Godzilla* was a metaphor for nuclear weapons; in *Minus One* Godzilla is a more general symbol of the devastation following WWII, and in particular the defeated Japanese government's utter inability to help its citizens deal with that devastation.

We follow a [failed kamikaze pilot](https://www.youtube.com/watch?v=4SSOTm0hEDQ&t=90s) participate in a citizen-organized effort to bring down Godzilla, while watching him piece together the wreckage of his personal life (his parents were killed in the fire bombing of Tokyo, and a woman and her adopted baby have moved in with him in his home). Then Godzilla arrives and rampages through Ginza, a district of Tokyo. With both the American military unable to help due to tensions with the Soviets, and the Japanese government unwilling to lift a finger, the citizens of Tokyo band together to deal with the monster themselves.

*Battle Royale*—the 1999 Japanese dystopian novel about a group of high school students forced to fight to the death on an island in an alternative-history totalitarian Japan is now probably most famous in the US as the book that Suzanne Collins ripped off when writing *The Hunger Games*. But just like *Godzilla Minus One* is a superior work than any American *Godzilla* movie, *Battle Royale* is better than its US-counterpart. It's gorier, more violent, maybe equally thrilling—but most importantly it has a better handle on the workings of its totalitarian government. In *The Hunger Games*, the games exist as entertainment for the wealthier districts; in *Battle Royale* the games are suggested to persist due to dystopian, bureaucratic inertia:

> Look, the government is run by nothing but idiots. Not only that, but you can't get into government unless you're an idiot in the first place. I think whenever this lovely little game was concocted—probably by some lunatic military theorist—nobody said a word against it. Butting in on the business of the experts only brings trouble. And in this country, it's terribly difficult to discontinue something once it's been established. Stick your nose into something that doesn't concern you, and you'll be out on your ass. Or maybe you'll be sent to a labor camp on charges of deviant ideological tendencies. Even if nearly everyone is against it, nobody says anything. And so nothing changes. There are a lot of screwed up things in our country, but they're all structurally the same. It's textbook fascism.

I had a little trouble keeping through of the names of all 40 classmates (perhaps Japanese names are harder for me to remember than American ones), but other than that I thought the translation was surprisingly readable.

Further recommendations:
* If you're not going to watch *Godzilla Minus One*, at least listen to [its booming score](https://open.spotify.com/track/5f9XWGwkfNHVd8c8Li8C5A?si=7a08249feb1240d2).
* I quite enjoyed *Monarch: Legacy of Monsters*, an Apple TV+ show set in Legendary's [MonsterVerse](https://en.wikipedia.org/wiki/MonsterVerse). Watch it for the kaijus, but stay for the surprisingly good story.
* Obviously you should check out [the film adaptation](https://www.imdb.com/title/tt0266308/) of *Battle Royale*.

# *American Fiction* / *Less*

It's striking how similar the plots of *American Fiction* and *Less* are—in both works, the protagonist is a struggling writer who enjoyed moderate success earlier in his career, but whose current novels are not selling. Their writing problems parallel their personal problems: unlucky in love, adrift in middle age. The protagonists embark on a journey of self-discovery to bring them closure in both their professional and personal lives.

This is mostly a testament to the subjects writers tend to write about (themselves—*American Fiction* was based on [a novel](https://en.wikipedia.org/wiki/Erasure_(novel))). In truth, the two works are dissimilar beyond their framing. *American Fiction* is—like *Tár*—concerned with the relationship between art and the artist. See—Monk (the novelist protagonist) is black, but he doesn't think he writes *black* books. (In a very funny scene he visits a bookstore and hauls his books from the African-American section to Historical Fiction). To prove a (admittedly muddled) point, he pens a stereotypical "black" book titled *My Pafology* and submits it to his editor. Of course, it succeeds wildly and is immediately bought by a publisher with Hollywood interested in a movie adaption. While Monk navigates his newfound literary success he also must deal with the death of a close sister, an aging mother with dementia, and an estranged brother.

The underlying tension about whether it's good or not to lean into stereotype to please white audiences is never fully resolved over the course of the film, indeed the ending falls a little flat. However the humor more than makes up for this—my theater was having a rollicking time.

*Less* deals a little with authorial identity (at one point its protago-novelist ponders whether he is a "bad gay" because his books are insufficiently committed to the homosexual agenda), but it's mostly a book about coming to grips with middle age. The premise is this: to escape the looming wedding of an ex-boyfriend, Arthur Less accepts a series of invitations that take him on a year-long journey encircling the globe—to New York City, to interview fictional sci-fi author H.H.H. Mandern, then to Mexico to attend a conference on his literary mentor; to Turin to attend an award ceremony, to Berlin to teach a creative writing seminar, to Morocco where he intends to celebrate his 50th birthday, to India to finish up his current book, and finally to Japan to write an article for a travel magazine about Kaiseki cuisine.

*Less* is one of the most distinct Pulitzer-Prize winning novels you'll read—unlike its fellow awardees it's light, satirical, and (like *American Fiction*) laugh-out-loud funny. Greer has a gift for simile—the traffic-jammed highways of Mexico City are like "the rivers of red taillights conjur[ing] lava flows that destroyed ancient villages"; the coffee-maker in a hotel room is "a hungry little mollusk, snapping open its jaws to devour pods."

Greer succeeds at giving his protagonist an enduring humanity; as a New York Times review puts it: "By the time Arthur reaches Japan, the reader isn’t just rooting for him but wants to give the poor guy a hug."

Further recommendations:

* [*Yellowface*](https://www.harpercollins.com/products/yellowface-r-f-kuang) by R.F. Kuang is also about racial diversity in the publishing industry
* Greer wrote a sequel to *Less* called [*Less is Lost*](https://www.hachettebookgroup.com/titles/andrew-sean-greer/less-is-lost/9780316301398/?lens=little-brown)
