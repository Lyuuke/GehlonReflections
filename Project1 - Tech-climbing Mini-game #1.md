# The Concept
It's not easy to visualize and make players enjoy the process of tech researching. I'm using the word "climbing" here due to a certain old backstory which abstracted human being's complete lore as a tree, more precisely, a *Quercus dentata* oak tree. In this perspective, studying into new fields of science (or other fields, such as witchery or branches of occultism) is no more than climbing the tree. Not everyone will enjoy climbing trees, but as tech research plays an essential part of this mod, we should do something to provide a more vivid, playable and plausible realization to convert the process into an interactable "recipe" (in some GUIs) that players could act on.

In many currently available attempts, this would point to mini-games. Maybe you are familiar with the research mini-games of Thaumcraft, it's just something like that.

# Outline
This is one of several research mini-games I came up with (*The python realization will be available in maybe a few weeks*). The only operation in this game requiring the player to do is clicking squares in a grid.

## Board
The game will start with a 9\*9 (or maybe bigger, such as 12\*12 or 16\*16) grid. All squares in the grid has two states, just like two sides of a coin, we call them *"head" (H)* and *"tail" (T)*. The initialization of the board randomly set every square of a head or a tail. Let me use a smaller 5\*5 grid for demonstation (where "X" denotes T, while blank denotes H):
```
┌─┬─┬─┬─┬─┐
│ │ │X│ │G│
├─┼─┼─┼─┼─┤
│X│ │ │ │ │
├─┼─┼─┼─┼─┤
│ │ │ │X│ │
├─┼─┼─┼─┼─┤
│ │ │ │ │X│
├─┼─┼─┼─┼─┤
│S│ │ │X│ │
└─┴─┴─┴─┴─┘
```
Two of the corners, located upper-right and lower-left are *"start"* and *"goal"* (as marked above with "S" and "G") in the game. The only thing a player should do is *to flip the squares so that T squares will pave a path from S to G*.

Obviously, it will be too easy to solve (and however not interesting to play) if the player could flip the squares one by one, thus there should be some mechanisms to lift restrictions and expenses to be paid. Two basic rules should be added:
1. Solution should be given within certain number of steps;
2. Every operation will flip all the squares (more than one) in a certain area at once.

For example, we can set the area flipped of a single operation "+" shape (the suqare clicked plus four neighboring suqares), so a normal operation will work like this:
```
┌─┬─┬─┐    ┌─┲━┱─┐
│X│ │ │    │X┃X┃ │
├─┼─┼─┤    ┢━╃─╄━┪
│ │X│ │ -> ┃X│ │X┃
├─┼─┼─┤    ┡━╅─╆━┩
│ │X│X│    │ ┃ ┃X│
└─┴─┴─┘    └─┺━┹─┘
```
Where all the squares inside the bold "+"-shaped frame are flipped.

## Additional Rules
The game may still be too easy or boring will just two rules given above, so different rule sets could be added according to needs. Such as:
- Randomly chosen squares on the board should be in a certain state (H or T) in the end;
- Random edges are chosen in the beginning, and the two squares sharing this edge must be in different states in the end (such as H|T or T|H, but H|H or T|T is not allowed).
- "Damaged" board, with some of the squares unavailable to flip.

With there rules implemented, I would call the game "Tech-climbing Mini-game Prototype", or TCM-P.

## Simple Topology Game
Now I only retain the flipping gameplay (abandoning the start-to-goal paving game), to turn this game into something that implicates some basic topological ideas. Matching the *genus* of two 2D shapes through flipping may be friendly enough to play.

Firstly, a closed 2D figure will be given as reference. Player must flip the squares to pave a curve, not to connect two corners this time, but to form a closed figure sharing the same value of *genus* with the given figure. For example, we start with a "O" shaped figure, and do the puzzle:
```
┌─┬─┬─┬─┬─┐
│ │X│X│X│X│
├─┼─┼─┼─┼─┤
│X│X│ │X│X│
├─┼─┼─┼─┼─┤
│X│ │ │X│X│
├─┼─┼─┼─┼─┤
│X│X│X│X│ │
├─┼─┼─┼─┼─┤
│ │ │ │X│ │
└─┴─┴─┴─┴─┘
```

On this board, T squares encircled an area and formed a figure with *genus = 1*, identical to the "O" shape. However, if the given figure is "8"-shaped (whose *genus = 2*), then the board above will not match.

This topology-related version will be called "Tech-climbing Mini-game of Topology", or TCM-T.

# Program Realization
## TCM-P

The difficulty in TCM-P depends on whether a "path check" should be visually shown to the player, *i.e.* so that players could see the *shortest path* connecting S and G in their solutions. This is envolved with the *shortest path problem of an undirected graph*, which requires some graph theory basics to ponder on. I'll just skip this for now because of my yet poor graph theory knowledge.

Despite this problem, TCM-P is actually quite simple. Just start from a set containing only S (if it is T), and append every T suqares neighboring to the elements in the set, to the set itself. Finally we will obtain all the T squares connecting to S, then the solution passes if and only if G is in the set.

## TCM-T

As for TCM-T, the main difficulty is how to determine the *genus* of a figure. We should start from clustering all the T squares on the board into several sets, where squares in the same set connect to each other, while do not dirrectly connect to the ones in other sets. Then traverse the H squares and inspect the rows and columns in four directions:
```
     * this col
┌─┬─┬─┬─┬─┐
│ │ │X│X│X│
├─┼─┼─┼─┼─┤
│X│X│ │X│X│
├─┼─┼─┼─┼─┤
│X│ │#│X│X│ * this row
├─┼─┼─┼─┼─┤
│X│ │X│X│X│
├─┼─┼─┼─┼─┤
│ │X│X│ │ │
└─┴─┴─┴─┴─┘
```

If this H square is *enclosed* by a figure F, we can expect that *the first T squares encountered by going towards four directions starting from this H square are 4 elements of set F*. If there is no T square in any direction, or four T squares encountered belong to different T square sets, then we abandon it and inspect the next H square.

Once we find an *enclosed* H, it will be easy to sort out all the H's connecting to it, which constitute the whole area that figure F encloses. We just skip these squares and continue the traverse.

