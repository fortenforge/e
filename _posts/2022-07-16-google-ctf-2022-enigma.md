---
layout: post
title:  "Google CTF 2022: Enigma (373 pts)"
date:   2022-07-16 3:12:44 -0500
categories: ctfs crypto
use_math: true
tags: ctfs crypto
---

**Enigma** was a cryptography challenge in the 2022 Google CTF in which the goal was to decrypt a message encrypted with the [Enigma machine](https://en.wikipedia.org/wiki/Enigma_machine). 

The cracking of the Enigma machine by Allied codebreakers during World War II burst into popular culture with the 2014 film [*The Imitation Game*](https://www.imdb.com/title/tt2084970/) (a fine film, but rife with historical inaccuracies). I myself first learned about it much earlier when I read Simon Singh's fantastic [*The Code Book*](https://www.amazon.com/Code-Book-Science-Secrecy-Cryptography/dp/0385495323) which describes the history of cryptography from the Caesar cipher to RSA, and devotes a chapter to the cracking of the Enigma.

As the README of this challenge explains, Alan Turing and his fellow codebreakers at Bletchley Park never did manage to completely break the Enigma. Instead they developed what in modern cryptography we'd call a known-plaintext attack, in which they made assumptions about particular words and phrases (they called them "cribs") that appeared early in the plaintext and leveraged those guesses to discover the key. In practice this was good enough to decrypt a substantial number of German messages, but what this challenge asks us to accomplish is what Turing did not: decrypt a German message without any knowledge about its plaintext.

<!--more-->

## How Enigma works

I won't spend too much time explaining how the Enigma machine works as there are countless explainers online that can do a better job than I can, but will highlight some of the main points. At its heart, the Enigma machine is simply an enormously complicated circuit that wires each keyboard letter to a corresponding lighted letter, such that when you press the key A, the G light might turn on; this means that A encrypts to G. Basically, it's just a substitution cipher. What makes it tricky though, is that every time you press a key, a rotor advances which switches up the circuit and changes the substitution key (making it a *polyalphabetic* substitution cipher)

![image](https://user-images.githubusercontent.com/7545794/179327410-8f6b6d5f-ce38-419e-8fdb-1a6ad153eefa.png){: .center-image }

There are three rotors each of which act as an individual permutation of the letters A-Z, and each of which leads into the next one. Each rotor has 26 different positions, each of which yields a different permutation. Every time a letter is pressed, the rightmost rotor advances by 1 position. 

In addition, each rotor has one or more 'notches' that can cause the rotor to its left to advance by one position. For example, if the notch for the rightmost rotor was positioned at the letter 'C', and the rightmost rotor stepped from C → D, then the middle rotor would also advance by one step. In practice this means that the middle rotor advances every 26 letters, and the leftmost rotor advances every \\(26^2\\) letters. In particular, this means that the period of the machine is \\(26^3 = 17576\\). This means that it would take 17567 letters before a substitution key is repeated. 

Enigma machines came with a set of up to 8 pre-configured rotor wheels, of which 3 were chosen in a certain order for that day's messages. The rotor wheels were numbered using roman numerals, from I to VIII. In addition to the choice of the which wheels to use, each rotor had 2 additional choices: its starting position as well as the position of the notch relative to the rotor's internal wiring (the so-called "ring setting").

If the Enigma machine solely consisted of the rotors it wouldn't actually be all that secure; there are few enough choices for the rotors and their initial positions that its pretty straightforward to bruteforce all possible combinations until you find one that produces the plaintext.

To counter this, the Enigma designers added the *plugboard* which allowed you to swap pairs of letters before and after they entered the rotor system. For example if there was a plugboard wire connecting R and P, then when you pressed the letter P it would first get turned into an R before entering the rotor system. Although up to 13 different plugboard wires could be used at a time, in practice only at most 10 were used.

![image](https://user-images.githubusercontent.com/7545794/179327503-0e739344-45b6-40c9-87a8-6e992fdc9c86.png){: .center-image }

The plugboard enhanced security of the machine by dramatically increasing the number of key settings. For 10 plugboard wires, the number of possibilities is:

\\[\frac{26!}{6! \cdot 2^{10} \cdot 10!} = 150,738,274,937,250\\]

well outside the possibility of bruteforce, even by modern standards.

A typical Engima key setting looks like this:

`VI III II NRS AO BH CU DL FM GW JZ KY PX QV XKR`

The first three terms indicate which rotor wheels were chosen and in which order; here they've chosen wheels VI, III, and II. The next term indicates the ring settings for each of the wheels; the first wheel is set to N, the second to R and the third to S. The next 10 terms represent the 10 plugboard switches. For example, A is swapped with O, B with H and so on. Finally the last term indicates the initial position of each rotor wheel (X, K and R respectively).

## Attempting the challenge

I had the good fortune to have watched [this Computerphile video](https://www.youtube.com/watch?v=RzWB5jL5RX0) several weeks before the competition in which Dr. Mike Pound explains exactly the problem we're interested in: how to break an Enigma machine without known plaintext, but with access to fast computers. Even better: Mike provided [his code](https://github.com/mikepound/enigma) for doing so!

Here's how his approach works: the key observation is that if you get some of the settings of the machine correct, even if your other settings are wrong, your output will still be closer to valid English (or in our case, German). So, if we had a way of measuring how close a string of text was to German, we could bruteforce some of the settings until we found the best candidate, and then bruteforce the remaining settings.

Mike's code comes with a handful of different metrics for ranking candidate decipherments. One of them is the *index of coincidence*, a remarkable measure invented by celebrated American cryptographer William Friedman. It's simply the probability that two randomly chosen letters from the text happen to be the same. For totally random text, you'd expect a probability of:

\\[1/26 = 0.03846\\]

but since English (and German) text has some letters with much higher probability of appearing than others, their index of coincidence is substantially higher (0.067 for English, 0.078 for German). What makes the IoC a particularly useful metric for cryptanalysis is that it's invariant under letter substitution. It's easy to see that if you applied a substitution cipher to a text (replacing all A's with D's, B's with X's, and so on), the index of coincidence would not change. 

This makes the metric suitable for use in cracking the Enigma. Specifically, if we're trying out different rotor settings and happen to hit upon the correct one, the resulting decrypted plaintext should have a higher index of coincidence than purely random *even if we have none of the plugboard settings correct* because the plugboard mostly amounts to substituting between pairs of letters.

So, the approach at a high level is as follows:
 * Bruteforce all possibilities for the rotor settings assuming an empty plugboard, and for each, measure the resulting index of coincidence
 * Pick the setting that yields the greatest IoC and use an iterative hill-climbing approach to find plugboard settings (trying all possibilities for an additional plug and choosing that which yields the greatest IoC improvement)

## Optimizations

The number of rotor settings to bruteforce is naively:

\\[8 \cdot 7 \cdot 6 \cdot 26^3 \cdot 26^3 = 103,795,700,736\\]

(\\(8 \cdot 7 \cdot 6\\) choices for the particular rotors to use, \\(26^3\\) choices for the rotors' initial positions, and \\(26^3\\) choices for the ring settings for each rotor).

This is far too high, so we need some way to cut this down to a manageable number. The first observation we can make is about the ring settings, which as you'll recall allows the operator to rotate the internal wiring of the rotors relative to the alphabet ring on the edge of the rotor. For example, suppose the ring setting was at 2. This means that an input of A would first get rotated to an input of say, C before entering the wiring for C and coming out as, say a Q.

Note that this is basically equivalent to having a ring setting of A=0 but setting the rotors initial position to 2; the outcome for an input of A would be equivalent. What, then is the difference between the ring settings and the rotor settings? Why does the Enigma specify both? The difference comes down to that pesky notch we mentioned earlier. If a rotor has a notch at the letter C, advancing the initial position of the rotor by 2 advances the notch as well, bringing it two steps closer to turning over the adjacent rotor, but advancing the ring setting by 2 leaves the notch in the same place.

What this means is that aside from the complication introduced by the notch, the ring settings are unimportant and can mostly be ignored / handled by the initial ring position. For example, if the correct ring settings were ABC and the correct initial rotor positions were DEF, then a ring setting of AAA and initial rotor positions of A + D, B + E, C + F = DFH would produce nearly the same plaintext, albeit scrambled a little bit whenever the notch caused a middle or left rotor turnover at not quite the right time. 

This allows us to cut down the number of settings by a factor of \\(26^3\\). Not bad, but we can do even better: if we carefully read the README, we'll notice that it mentions that they constrained which rotors were chosen in a particular way:

> Germany generated its codebooks by rolling dice. However, it enforced rules meant to ensure there was "enough variation" between days. If those rules were violated, dice would be rerolled. Those rules are:
> - Every day must include at least one "naval rotor" (rotors VI-VIII).
> - The same naval rotor cannot be used in the same position on two consecutive days.
>
> These constraints apply in this challenge.

The challenge simulated a week's worth of messages, 1 per day from May 9th 2022 to May 15th 2022. The flag was sent in the message on May 12th. May 11th's key settings were:

`III V VIII PLM UX YC TV RB AP QL ID GH ZK JF WEB`

and May 13th's key settings were:

`V VI II ZFK EM YQ JT VN DH BP ZO XU RG LC RTN`

From this we know that we can excluding any settings that include 0 naval rotors (\\(5 \cdot 4 \cdot 3\\) choices), any settings that include rotor VIII in the last position (\\(7 \cdot 6\\) choices), and any settings that include rotor VI in the middle position (\\(7 \cdot 6\\) choices). This leaves us with 198 choices for the rotor wheels, down from 336.

We're left with:

\\[198 \cdot 26^3 = 3,480,048\\]

settings to bruteforce—certainly manageable on modern hardware.

## Launching the attack

The first thing I did was verify that Mike Pound's code could correctly decrypt the May 9th and 10th messages when provided with the correct settings. I further verified that the index of coincidence did indeed slightly rise when the rotor settings were correct but the plugboard settings were wrong. The difference is quite subtle—an index of 0.040 vs 0.038 for example—but it does exist and is enough to exploit.

I then modified Mike's code to bruteforce all possible rotor choices and initial rotor positions, leaving the ring settings as AAA and the plugboard as empty, and for each one measured the IoC for the decrypted ciphertext, and saved the top 300 candidates. I knew this would take several hours to complete, so I let it run while I worked on other challenges.

The results didn't look particularly promising—there wasn't any particular setting that seemed much better than the rest, but I figured I might as well continue. For each of the top 300 candidates I bruteforced all possible ring settings (\\(26^3\\)), chose the best one and then ran the hill-climbing attack to find the best plugboard settings. Remarkably, exactly one candidate produced a plaintext with a very high index of coincidence after this, and when I looked at the plaintext, it was clear that it was very close to correct:

```
KHEBEWETTBEWERBERYDIEFLAGAIZEGINNTMITEINEMGROSSENJCWLSINEMGROSSENJTJUNDJFJXDADXFFFOLGTEINEOFFENEGESC
HWIEESNEKLAMMERXDERLETZTEBUCHNUCBEISTEINEGESCHLOSSENEGENKAWIFFENEKLAMMERXZWISCHENNQGSENKLAMMERNSTEHE
NEINIGEARKEREBUCHSTABENXDERERSTEBTVRSTABEZWISCHENDENKLAMMERXNXTJDJXDERZWEITEBUCHSTABELQTSCHENDENKLAM
MERNISTJUJXYLPDRITTEBUCHSTABEZWISCHENEPGKLAMMERNISTJHJXDERVIERTRBKCHSTABEZWISCHENDENKLAMMMLQISTJAJXD
ERFUENFTEBUCHSTWCUZWISCHENDENKLAMMERNISTJNJLDARAUFFOLGTJTGEJXDANACHNNBMSTTOMYUQLPTWWUCTPDSAWGGPZLQMF
BUTTAJNZBVUTFOQWFDMAUNMCYQYTOGFJZOTIDQPLGDVRFIHXPZAETKQOFUQTYNSBWNRQGOLKJSKYSCDFSJGDOALMECOLWRKYSEON
FXXLHOPEIDFOIAJGXIOQWJAPYCISTEINEGROSSEERRUNGENSCVXITXDUKANNSTSTOLZAUFDICHSZEVXWIRHOFFENDUHASTGANZVI
ETDDASSBEIDEMGOOGLECTFINDIEWBGJAHRUNDWIRFREUENUNSDASSOOOITDABEIBISTXCAPTURETHEFHFLWETTBEWERBESINDWET
TBEWECLEWOABSICHTLICHVERWUNDBARPDOOGRAMMEVONSPIELERNFUERPNQCTEGEHACKEDWERDENXMEISTEKZMABENDIESEWETTB
EWERBEVERMUNIEDENEKATEGORIENUNDEINDVHWMISCHESPUNKTESYSTEMXESWGRLINDERREGELZWISCHENZWEIVZPQCHIEDENENA
RTENUNTERSCHIUZXNXXDERSOGENNANTEJEOPARDDWWILYWODIETEAMSUNDDIESPIEMWDVERSUCHENEINEZENTRALECHPDJENGEZU
HACKENUNDDEMATTACPQHFELBOZQWLUTRTMQTRFTONZCWAREBAPKAGGLNSRHGPIAZCNLEAGDWOAKDPVNYULLOZWQJXZAPPPGEYAMZ
XCMNXSSYLNRQJGOXSTDKMUSJRAFVGNOVTEYLDASMMCCNYGOMMEVRUEIISHVCQVOCSHPXNCFQOTRXOHENPREISGELDERNXDIESEWE
BBBEWERBEEIGNENSICHBESONDKIZGUTUMINDIEITSICHERHEITSKRFNCHEEINZUSTEIGENUNDSCHNCSINEUEFERTIGKEITENZULE
RNEDYKIEVERSCHIEDENENKATEGORIBUENFOKUSSIERENAUFVERSCHIEWSTEGEBIETEDERITSICHERHEITTSBZUMBEISPIELKRYPT
OGRAPHIDZGEBSICHERHEITYBINARYEXPLQOGATIONUNDREVERSEENGINEERNVRXDIEMEISTENCTFWETTBEWERBBWAUERNSOUMDIE
VIERUNDZWANTQFBISACHTUNDVIERZIGSTUNDEVLTVERSCHIEDENENSPIELERNINBXCSCHIEDENENZEITZONENDIETCXJNAHMEZUM
ERMOEGLICHENXDEHYHSTDIESESTEXTESISTNICHTWERXTIGUNDISTEINZIGUNDALLEIDDDYKATZWQYELEXNUSEFSGRJBPAURNYMT
JEJGIPLEXGPUAIVTGGHXOSNUHLRFGAPERNNBGDAFHBVBWLKTKTCXAEYQFLOUIFMBWMMJBTFGYSGSLHKVXBIFBERBYCBEPJUUKJTS
OVDZBJRYMCSATTVIDSBPRNRBJHHALTZULESENXDASISTEINEELQQSTARKELEISTUNGXHOFFENTLOJGKANNSICHDEINTEAMFUERDA
SHAPALEQUALIFIZIERENXWIRWUEHQFNUNSFREUENDICHDORTZUSEHUYFALSOBISBALDUNDNOCHMALHEIXQICHENGLUECKWUNSCHX
WIESCQRXVORHINERWAEHNTISTDERRESPCHWICHTIGXDUKANNSTNUNWIRKUGHHAUFHOERENZULESENXHIERGUSPESNICHTSMEHRZU
SEHENXALSEHHRKLICHYWARUMLIESTDUIMMEPHKCHDIESENTEXTYSOLLTESTDULHSHTANDEREAUFGABENLOESENXJRCGLAUBEDUKA
NNSTDEINEZEITREFSERNUTZENINDEMDUANDERESRBVENMACHSTXVIELLEICHTWAERCJWNEPAUSEAUCHGANZSINNVOLLOKASOLLTE
STAUCHNICHTVERGESEZKGQCEBRORLPJDYLOXHLRPKAHDEPVUQVZQINDWRWERTZMXKDKUPPFTTWQCTXELUJCVKSLNQHPEPIKXOSUM
AFSLNWQAFIFALNDRDSILVUYGJJKPOAYHYKEVVXOCHQHIZCEHTGRSYLZSSVVGHZJMGCRYZOTIYBBOIERUNWICHTIGISTXHIERGIBP
HNNICHTSNEUESMEHRZULESENXHAATEHOERAUFNUNX
```

Note that you can see clearly valid strings like `REVERSEENGINEER` and `BINARYEXPLQOGATION` and `GOOGLECTF` inside. However, it wasn't quite right. I didn't have exactly the right settings. 

I obtained this partial plaintext at around 4 in the morning on Sunday and was fairly tired; perhaps had I been thinking more clearly I could have pinpointed the problem: the garbled bits were a result of incorrect notch turnover points. I needed to go back and adjust the ring settings and initial rotor positions simultaneously to correct the errors. However, I was too fatigued to realize this and instead thought that one of the plugboard switches that I had found was wrong. I spent the next couple hours fruitlessly debugging the plugboard before giving up.

Luckily, my teammates came to the rescue with a different approach.

## Reading garbled German with no spaces or punctuation

If we had gotten exactly the right settings, the organizers had provided a way to derive an AES key from the settings and decrypt a properly formatted, punctuated version of the plaintext which we could just read to get the flag. We didn't have exactly the right settings though, so instead we decided to try to read the garbled German plaintext. How hard could it be?

As it turns out, it was very hard, but not impossible. With some massaging and heavy use of Google Translate's suggestions we eventually got to something like this:

```
KHE BE WETTBEWERB ERY DIE FLAG AIZE GINNTMIT EIN EM GROSSEN JCW LSINEM GROSSEN JTJ UND JFJ X DAD X FFFOLGT EINE OFFENEGE SCHWIEES NE KLAMMER X DER LETZ TEBUCHNUCBEIST EINE GESCHLOSSEN 
EGENKAWIFFENEKLAMMERXZWISCHENNQGS
EN KLAMMERN
STEHENEINIGEARKEREBUCHSTABENXDERERSTEBTVRSTABEZWISCHENDENKLAMMER X N X TJDJ
X DER ZWEI TEBUCH STABEL QTSCHEND  EN KLAMMERN IST JUJ
XYLP DRITTE
BUCHSTABE ZWISCHEN EPG KLAMMERN IST JHJ X
DER VIER TR BKCHSTABE
ZWISCHEND ENKLAMMMLQ IST JAJ X
DER FUENFTE BUCHSTWCU ZWISCHEND ENKLAMMERN IST JNJ L DARAUF FOLGT JTGEJ
X
DANACH NNBMT JTANJ X

WEITERHIN
KOMMT
NMLUY
WAS J WOR AUF JTURINGJ FOLGTL
DIE LETZTEN
BUCHSTABEN
SINDOSSY
JIJ YJCHTKONNTEJX
DU HAST
BGVENIGMAGE KNACKTOHNEZUGRITITUF EINEN KLARTEXT ZUHABEN YRGGQSTEINE GROSSEERRUNGEN SCVXITX DU KANNST STOLZ AUF DICHS ZEV
X
WIR HOFFEN DU HAST GANZVIETD DASSBEIDEM GOOGLECTF IN DIE 
WBGJAHRUNDWIRFREUENUNSDASSOOOITDABEIBIST X CAPTURETHEFHFL WETTBEWERB
ESINDWETTBEWECLEWOABSICHTLICHVERWUNDBARPDOOGRAMME VON SPIELER NFUERPNQCTEGEHACKEDWERDENXMEISTEKZMABENDIESEWETTBEWERBEVERMUNIEDENEKATEGORIENUNDEINDVHWMISCHESPUNKTESYSTEMXESWGRLINDERREGEL ZWISCHEN
```

Which translates as:

```
KHE BE COMPETITION ERY THE FLAG AIZE STARTS WITH A LARGE JCW LSINEM LARGE JTJ AND JFJ X DAD X FFOLLOWS AN OPEN BREAK NE CLAMP X THE LAST BOOK NUCBE A CLOSED
EGENKAWIFFENKLAMMERXBETERNNQGS
EN BRACKETS
ARE SOMEARKERLETTERSXTHEFIRSTBTVRBARBETWEENBRACKET X N X TJDJ
X THE SECOND BOOK STABEL QTSCHEND EN BRACKETS IS JUJ
XYLP THIRD
LETTER BETWEEN EPG BRACKETS IS YHJ X
THE FOUR TR BKLETTER
BETWEEN ENCLAMMLQ IS JAJ X
THE FIFTH BOOKSTWCU BETWEEN PARENTHESES IS JNJ L FOLLOWED BY JTGEJ
X
THEN NNBMT JTANJ X

FARTHER
COMES
NMLUY
WHAT J WOR FOLLOWS JTURINGJ
THE LAST
LETTERS
SINDOSSY
JIJ YJCHTKONNTEJX
YOU HAVE
BGVENIGMAGE KNACKTOHNEHRITITUF HAVING A CLEAR TEXT YRGGQSTEINE GREAT ACHIEVEMENTS SCVXITX YOU CAN BE PROUD OF YOURSELF ZEV
X
WE HOPE YOU HAVE GOOGLECTF IN THE
WBG YEAR AND WE ARE SOOOOO PLEASED TO BE PART OF X CAPTURETHEFHFL COMPETITION
ESARECOMPETIONSWHEREINTENTIONALLYVULNERABLEPDOOGRAM BY PLAYERS NFORPNQCTEHACKEDXMOSTEKZMATHIS COMPETITIONSMUNIEDENCATEGORIESANDEINDVHWMIXPOINTSSYSTEMXESWGRLINDERRULE BETWEEN
```

The README notes that J's are used to represent quotation marks, so this plaintext starts by telling us that the flag starts with a 'large' (or uppercase) "C", "T", and "F" followed by an open bracket {. So far so good.

Some more observations:
* The first letter between the brackets is a JDJ = "D"
* The second letter between the brackets is "U"
* The third letter between the brackets is "H"
* The fourth letter between the brackets is "A"
* The fifth letter between the brackets is "N" followed by "TGE"
* Then "TAN"
* Farther comes ...unintelligible
* What follows is "TURING"
* The last letters ...sindossy??? something something "I" then "CHTKONNTE"

Right... so it looks like we have something like `CTF{DUHANTGETAN???TURING???ICHTKONNTE}`

Putting this into Google Translate:

<img width="1293" alt="image" src="https://user-images.githubusercontent.com/7545794/179326943-5ad5e3f2-3bbd-4378-bf48-8a7f968ffa83.png">

OK, this is looking good! Something about how we've done what Turing couldn't. Let's error correct to `CTF{DU HAST GETAN TURING NICHT KONNTE}`. Google tells us that this translates to "you did turing could not"; seems like it's missing a word there. Let's try inputting "You did what Turing couldn't" going from English → German:

<img width="1292" alt="image" src="https://user-images.githubusercontent.com/7545794/179327061-839b0e91-df5e-4584-87f5-1ef13cc8cd07.png">

Seems worth a try! We submitted `CTF{DU HAST GETAN, WAS TURING NICHT KONNTE}` and it was correct 🎉!
