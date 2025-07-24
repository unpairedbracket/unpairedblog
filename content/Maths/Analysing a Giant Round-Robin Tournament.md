---
published: 2025-07-19
tags:
  - tournaments
  - wrestling
---

Over on tumblr, the Most Beloved AEW Wrestler Tournament: Round-Robin Edition recently finished its giant cycle of all 156 wrestlers facing each of the other 155. At the encouragement of some friends, I thought I'd have a bash at some data analysis.

The approach I'm going to take is as follows:
- Assume any given competitor can be described by a single "quality" (or maybe "belovedness") value $q_i$ (read as "the quality of the $i^\mathrm{th}$ competitor" - I'll label some general unspecified person with $i$, $j$ or $k$ throughout this)
- In a poll between competitor $j$ and competitor $k$, seen by $N$ voters, the number of votes each competitor receives ($N_j$) is a function of their quality $q_j$, their opponent's, $q_k$, and (obviously) $N$. I can express this as $N_j = v(q_j, q_k) \times N$, defining the function $v(q_j, q_k)$ as "the fraction of voters who vote for a competitor of quality $q_j$ when placed against a competitor of quality $q_k$".
- I then try and find a set of quality factors $\{ q_i \}$ which make the predicted results of every poll in the tournament as close as possible to the actual results.

Because I don't know what $N$ is for any given poll (it's possible someone saw a poll but decided not to vote), I'll use as my measure the percentage margin between the two competitors, i.e. the difference between their number of votes divided by the total number of votes cast. This ensures the number $N$ cancels out:
$$
f(q_i, q_j) = \frac{N_i - N_j}{N_i + N_j} = \frac{v(q_i, q_j) - v(q_j, q_i)}{v(q_i, q_j) + v(q_j, q_i)}.
$$
A simple candidate for the "vote function" is $v(q_i, q_j) = \frac{q_i}{q_i + q_j}$, which leads to 
$$
\frac{N_i - N_j}{N_i + N_j} = \frac{q_i - q_j}{q_i + q_j}.
$$
A disadvantage of this vote function is that it's scale-independent: doubling the quality values of everyone in the tournament would leave the result unchanged. It also doesn't allow for the possibility of someone deciding not to vote: $v(q_i, q_j) + v(q_j, q_i) = 1$, so $N_i + N_j = N$. Because I know some people chose not to vote in polls between two people they didn't like, it would be nice if the model could account for this somehow.

To that end, I use this more complex model: of the $N$ voters who _see_ a given poll, only a fraction of people equal to $q_j$ (i.e. $q_j \times N$ voters) would _consider_ voting for candidate $j$. Similarly a fraction $q_k$ would consider voting for candidate $k$. I'll say these fractions are independent of each other, so that $(1-q_j)(1-q_k)N$ voters choose not to vote at all. Then, there are $q_j (1 - q_k) N$ voters who would consider voting for $j$ but not $k$, so they all vote for $j$. Similarly, $q_k(1-q_j)N$ voters wouldn't vote for $k$ but are happy to vote for $j$, so they do. Of the remaining $q_j q_k N$ voters who are considering voting for both candidates, the simpler model presented above applies, so an additional $\frac{q_j}{q_j + q_k} q_j q_k N$  votes go to $j$ and $\frac{q_k}{q_j + q_k} q_j q_k N$ to $k$.

This results in a vote function 
$$
v(q_j, q_k) = q_j(1-q_k) + q_j q_k \frac{q_j}{q_j + q_k} = \frac{q_j^2 + q_j q_k - q_j^2 q_k}{q_j + q_k},
$$
and an objective function
$$
f(q_i, q_j) = \frac{v(q_i, q_j) - v(q_j, q_i)}{v(q_i, q_j) + v(q_j, q_i)} = \frac{q_j^2 - q_k^2 + q_j^2q_k - q_jq_k^2}{q_j^2 + q_k^2 + 2q_jq_k - q_j^2 q_k - q_j q_k^2} = \frac{(q_j - q_k)(q_j + q_k + q_j q_k)}{(q_j+q_k)(q_j+q_k - q_jq_k)}.
$$
The fraction of potential voters who actually cast votes is
$$
\frac{N_i + N_j}{N} = v(q_i, q_j) + v(q_j, q_i) = q_j + q_k - q_j q_k.
$$
Now, I can compute the value of $f_{jk} = \frac{N_j - N_k}{N_j+N_k}$ for all pairs in the tournament (taking care to ignore $j=k$) and compare it to the prediction $f(q_j, q_k) = \frac{q_j - q_k}{q_j + q_k}\ \frac{q_j + q_k + q_j q_k}{q_j+q_k-q_j q_k}$ for some guess at the set of qualities $\{ q_i \}$. I use Newton's method to try and find an optimal solution (in the least-squares sense, minimising $\sum_{j \neq k} \left[f_{jk} - f(q_j,q_k)\right]^2$). A basic Newton solver is a bit unstable for this problem (I think because of the dependence of $f(q_j,q_k)$ on overall scaling of the qualities being weak, which would be worse for the simpler model), so I remove the smallest singular values when computing the pseudo-inverse of the Jacobian matrix. It's not fast - it's a big matrix inversion per iteration! - but with that done it works quite nicely. The solution reaches an overall shape in just a few iterations, then very slowly reduces the overall magnitude of the qualities, seemingly forever. The correction term does shrink over iterations though, so I think it's converging towards something. I give it 10,000 iterations and then cut it off.

# Results: Rankings

The most obvious output from this process is the $\{ q_i \}$ themselves. After 10k iterations of Newton's method, we have a winner. It's Willow. This shouldn't be surprising. Her overall belovedness percentage clocks in at 69% (nice)

Full rankings here:
```
69.23%: Willow Nightingale
61.28%: Swerve Strickland
60.81%: Toni Storm
60.14%: Adam Page
59.21%: Orange Cassidy
58.06%: Kenny Omega
52.75%: Eddie Kingston
51.36%: Kris Statlander
43.93%: Konosuke Takeshita
42.92%: Will Ospreay
42.71%: Samoa Joe
42.06%: Julia Hart
40.00%: Chuck Taylor
39.89%: Harley Cameron
38.85%: Nyla Rose
38.55%: Athena
36.08%: Wheeler Yuta
35.64%: Jon Moxley
33.72%: Jay White
33.51%: Brody King
32.04%: MJF
31.46%: Jamie Hayter
31.29%: Queen Aminata
30.08%: Mariah May
29.89%: Kyle Fletcher
29.45%: Katsuyori Shibata
28.62%: Christian Cage
28.47%: Claudio Castagnoli
27.97%: Kyle O'Reilly
27.26%: Daniel Garcia
27.10%: Kazuchika Okada
26.69%: Matthew Jackson
26.55%: Hikaru Shida
26.28%: Bryan Danielson
25.62%: Anthony Bowens
25.46%: Mark Briscoe
24.06%: Evil Uno
23.99%: PAC
23.89%: Hook
22.61%: Abadon
22.48%: Nicholas Jackson
22.16%: Powerhouse Hobbs
21.10%: Buddy Matthews
21.04%: Jack Perry
20.87%: Kota Ibushi
20.65%: Anna Jay
20.41%: Mercedes Mone
20.35%: Skye Blue
20.23%: The Beast Mortos
20.04%: Kip Sabian
19.78%: Emi Sakura
19.74%: Thunder Rosa
19.54%: Danhausen
19.21%: Bandido
18.86%: Adam Cole
18.38%: Darby Allin
17.38%: Mark Davis
17.00%: Penelope Ford
16.76%: Trent Beretta
15.96%: Marina Shafir
15.95%: Komander
15.88%: Juice Robinson
15.85%: Riho
15.78%: Yuka Sakazaki
15.41%: Killswitch
15.10%: Matt Menard
14.71%: Ruby Soho
14.67%: Hologram
14.61%: Isiah Kassidy
14.33%: Austin Gunn
14.25%: Sting
13.60%: Roderick Strong
13.05%: Bryan Keith
12.99%: Colten Gunn
12.61%: Lee Moriarty
12.55%: Nick Wayne
12.46%: John Silver
12.46%: Mr Brodie Lee
12.30%: Ricochet
12.25%: Dustin Rhodes
11.78%: Malakai Black
11.62%: Deonna Purrazzo
11.57%: Dante Martin
11.48%: Big Bill
11.42%: Red Velvet
11.20%: Taya Valkyrie
11.13%: Angelo Parker
11.09%: Shelton Benjamin
11.03%: Cope
10.96%: Keith Lee
10.72%: AR Fox
10.35%: Serpentico
10.12%: Billy Gunn
 9.97%: Lance Archer
 9.58%: Max Caster
 9.10%: Marq Quen
 8.99%: Alex Reynolds
 8.85%: Angelico
 8.59%: Cash Wheeler
 8.57%: Bobby Lashley
 8.40%: Darius Martin
 8.32%: Leila Grey
 7.65%: Matt Taven
 7.61%: The Butcher
 7.60%: Kiera Hogan
 7.50%: Diamante
 7.32%: Leyla Hirsch
 7.03%: Brandon Cutler
 7.01%: Lio Rush
 6.85%: Johnny TV
 6.78%: Rey Fenix
 6.62%: Brian Cage
 6.47%: Wardlow
 6.46%: Mercedes Martinez
 6.17%: Luther
 6.05%: Ricky Starks
 6.04%: Matt Sydal
 5.89%: Dax Harwood
 5.55%: Lee Johnson
 5.46%: Tay Melo
 5.19%: Action Andretti
 5.16%: Bishop Kaun
 4.48%: Scorpio Sky
 4.40%: The Blade
 4.22%: Mike Bennett
 4.15%: Colt Cabana
 4.12%: Ortiz
 4.05%: Toa Liona
 3.98%: Griff Garrison
 3.98%: Preston Vance
 3.75%: Dr Britt Baker DMD
 3.71%: Michael Nakazawa
 3.68%: Jay Lethal
 3.63%: Peter Avalon
 3.49%: Shawn Dean
 3.28%: Jeff Jarrett
 3.25%: Rush
 3.02%: Dutch
 2.96%: Josh Woods
 2.92%: Dralistico
 2.85%: Vincent
 2.68%: Madison Rayne
 2.65%: Satnam Singh
 2.45%: Ariya Daivari
 2.38%: Tony Nese
 2.20%: Anthony Ogogo
 2.14%: Aaron Solo
 2.08%: Sammy Guevara
 2.03%: Paul Wight
 1.79%: Nick Comoroto
 1.77%: Rebel
 1.76%: Serena Deeb
 1.73%: Miro
 1.62%: Chris Jericho
 1.24%: Saraya
 1.09%: Kamille
```

# Analysis: Match-by-Match Performance

Now, we don't just have a set of quality factors for all of our competitors! That optimisation I did above? It's not perfect. For every given $j, k$ pairing, I've tried to get $f_{jk} - f(q_j,q_k)$ (these are called residuals) as close to zero as possible, but they can't all be zero at the same time because there are many fewer variables to tweak (the 156 $\{ q_i \}$) than residuals to minimise (of which there are over 12,000).

What this means however is that we can look at each match's residual as a measure of how surprising the result was, and in which direction. If $f_{jk} - f(q_j,q_k)$ is positive, it means that the vote share achieved by $j$ was higher than would be expected by just comparing their overall score and that of their opponent. By plotting these residuals per-competitor rearranged into time order, I can get an idea of whether anyone's popularity was changing over time. This is a similar analysis to what @livelaughlariat was doing above, so I'll look at some of the same people that she did.

I've not included linear fits/correlation coefficients here like she did, for a few reasons:
 - I think any trends or features that show up are convincing enough on their own just from looking at the smoothed trend line, you don't need Karl Pearson to tell you what your eyes see.
 - Several of the features I'll try to argue are present are more localised than a global linear fit would get you.
 - I am a computational physicist, not a real scientist. Statistical rigour is for cowards

![[Max Caster.svg]]

Max Caster shows a very pronounced growth over the course of the tournament: He starts out underperforming his expected vote shares by 0.4 (i.e. 40 percentage points!) and ends up overperforming by 20 points!

![[Mariah May.svg]]

Mariah starts off vaguely growing in popularity, then starts to slide as the rumours of her leaving AEW start to gain momentum

![[Nick Comoroto.svg]]![[Serena Deeb.svg]]

Not much of interest for Comoroto and Deeb

![[Kenny Omega.svg]]

Kenny pretty consistent as well

![[Will Ospreay.svg]]
![[Toni Storm.svg]]
![[Ricochet.svg]]

Ospreay and Toni don't move much, but Ricochet's popularity does seem to be improving a bit over the course!

![[Mark Davis.svg]]

Pretty big bump for Mark Davis when he briefly started appearing more, though that eventually died down when people stopped seeing him again 

![[Kota Ibushi.svg]]

And a big boost for Ibushi when he finally appeared again!
![[Tay Melo.svg]]

Which was also replicated for Tay!
![[Yuka Sakazaki.svg]]

A cute one to finish - maybe somewhat tenuous? - I think that bump for Yuka Sakazaki around day 110 corresponds pretty closely with her and Takeshita announcing their marriage

Also, as I keep referring back to the simpler "everyone votes" model I mentioned above: The graphs that method's residuals produced look exactly the same as these ones. I literally couldn't see a difference. Surprising, but it's nice that the results are robust to differences in the model like that!

Stay tuned for some Social Choice Theory!