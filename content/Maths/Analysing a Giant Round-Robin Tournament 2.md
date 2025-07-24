#wrestling #voting
Last time, I showed off some heavy-duty data analysis on the Most Beloved AEW Wrestler Tournament. This time I'm taking a different approach to the same thing.

Rather than trying to score everyone like I did last time, now I'm just interested in ordering them. The so-called Condorcet methods from social choice theory (voting and elections to you and me) say that one candidate beats another if they're preferred by them in a majority of voters' ballots. A lot of this terminology and theory is designed for "ranked choice" voting systems, in which each voter submits an ordered list of candidates from most to least preferred. In our case though, "preferred by a majority of voters' ballots" just means they won the poll. 

A "Condorcet winner" is one who beats every other candidate in this sense. A "Condorcet cycle" occurs when you can place several candidates in a circle such that every candidate won against the person immediately clockwise of them, for example. It's impossible to pick a winner in this scenario, as every person can be considered to have "transitively beaten" (i.e. beaten someone who has beaten someone who has beaten someone who has beaten...) every other candidate (and themself!). For example, if Eddie Kingston beat Swerve Strickland and Swerve Strickland beat Orange Cassidy, but Orange beat Eddie? That's a Condorcet cycle\footnote{spoiler: this happened}. You could also consider a draw to be one, but that's less interesting. 

Now, we can draw out all 156 wrestlers standing in a circle and draw an arrow from each person (except Kamille) to everyone they beat (because she didn't). I'm not going to do that but imagine I did. This is a kind of graph (in the maths sense of a collection of nodes and edges, like a network) called - appropriately - a tournament. 

There's an operation you can do on graphs like this called "condensation". It takes all the Condorcet cycles in the graph and "condenses" them down to a single node. Here's a diagram I stole (it's CC0 so I'm allowed) from [Wikipedia, created by David Eppstein](https://commons.wikimedia.org/wiki/File:Graph_Condensation.svg)
![[Graph_Condensation.svg]]
The big yellow circles and arrows are the condensation of the smaller graph.

If I apply a graph condensation (followed by transitive reduction, which removes all of the unnecessary arrows between nodes that aren't immediately adjacent) to the MBAEWWT, I get... this!
![[big_cycle.svg]]
Willow wins, then there's a little Condorcet cycle containing Eddie, Swerve, Hangman, Kenny, Toni and Orange in joint 2nd place, a big Condorcet cycle of... 146 people in joint 7th place, Saraya 154th, Chris Jericho 155th and Kamille 156th.

I'll zoom in on the little cycle. It's not arranged in a nice order that shows the cyclic nature, but you can see that you can go Eddie -> Swerve -> Hangman -> Kenny -> Toni -> Orange -> Eddie, which prevents me from ranking any of them above any other under only the Condorcet criterion.
![[small_cycle.svg]]
The same is also true of the "everyone else" cycle, but there's no point trying to show you that. What looks like a grey background there is actually around 10000 arrows. 

What now remains is to break some arrows to let us pull this out into something vaguely linear. I'll use the Schulze method, which is preferred by nerds all over the place.

The resulting graph gets rid of the cycles, but is now very very wide. For ease of viewing on modern screens I've plotted it with Willow at the top and Kamille at the bottom.

Which is to say...

Do you love the colour of the Most Beloved AEW Wrestler Tournament?

![[schulze.svg]]