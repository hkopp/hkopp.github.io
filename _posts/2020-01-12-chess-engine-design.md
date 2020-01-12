---
layout: post
category : Lesson
tags : [chess, programming, software design]
---

## Intro
Some time ago I thought that it would be nice to have a programming
project again. It should not be too simple, such as writing a sudoku
solver, and on the other hand it should not be too complex.
I thought about fluid simulations, but then discarded the thought, as
I am probably missing the background knowledge.

So, my thoughts came to a chess engine. As that may qualify for a nice
programming project, I thought very hard about the design of a chess
engine. This post is what I learned in the process. Note that parts of
it may be wrong. Maybe, I will soon embark on this adventure.

## Overview
A chess engine is a computer program that can play chess. An
artificial intelligence, if you want to call it like that.
The current state is that Chess can be played by computers better than
by humans. In 1996, there was the famous match, where [Deep
Blue](https://en.wikipedia.org/wiki/Deep_Blue_(chess_computer)), a
computer program, won against the chess world champion Gary Kasparov.
Nevertheless, chess is far from being a solved game. For example, it
is not known if for optimal play, the first player always wins or if
the game ends in a draw.

In order for a program to play chess, there need to be several
modules which will be explained in detail in the following.
There needs to be a representation of the board, as well as knowledge
of the rules. 
Then, the game tree needs to be computed. This tree contains a board
state at each node, and the links between the nodes are the valid
state transitions, i.e., the valid moves. Note that this tree is too
large to be fully computed. Thus, it can only be partially computed. Additionally, note that the board representation needs to
be small in order to create this tree fast.
Next, we need an evaluation function which helps us to evaluate the
different game states. We need a search strategy in order to search
through the partial tree and find the best move.
Finally, an interface is needed which is used to communicate with the
chess engine.

## Board Representation and Game State
A chess board is exactly 8x8 fields large. As a funny coincidence,
current CPUs have registers of 64 bit length. Consequently, a bunch of
optimizations are possible, as 8*8=64.
Each piece can be represented on an own board. This is 64 bits, where
all bits except one of them are zero. The remaining bit that is one
describes the position of the piece.
These boards are called [Bitboards](https://www.chessprogramming.org/Bitboards).

Using various Bit-fiddling approaches, the valid moves can be
computed.
Note that this is easy for knights, as their movement cannot be
obscured by other pieces. Other pieces such as a rook, on the other
hand cannot jump over other pieces. Therefore their implementation is
slightly trickier, as pieces in their path need to be taken into
account.
One technique for that are [Magic](https://www.chessprogramming.org/Magic_Bitboards) [Bitboards](https://rhysre.net/2019/01/15/magic-bitboards.html).

Additionally to the board, the pieces and their movements, there are
various special moves that need to be taken into account in order to
go from a board with positions to the game state. The following come
to mind:

- en passant
- 50 move rule
- threefold repetition
- castling

### En Passant
This is a special move that can only be performed by pawns, but only
dependent on the movement on another pawn.
[Wikipedia](https://en.wikipedia.org/wiki/En_passant) probably has a
better explanation.
I think that en passant can be implemented by using an additional en
passant bitboard that describes the fields to which an en passant move
is possible.

### 50 Move Rule
If for 50 moves, there was no capture, the game is declared a draw.
This can be implemented by an additional clock in the game state that
counts the moves and is increased for each move. When a capture
happens, it is reset to zero.

### Threefold Repetition
If a board state is encountered three times, the game is declared a
draw. In order to implement this rule, the game state needs to
remember the last positions. This is bad, as that probably takes a lot
of memory and thus is slow.
The last positions can be pruned, if a piece is captured.
There are various [better
ways](https://www.chessprogramming.org/Repetitions) to implement this.
I have not researched much into this topic, but apparently it is
possible with a [special hash
table](https://en.wikipedia.org/wiki/Zobrist_hashing).
Well, this is probably the special rule that will need the most lines of
code for extra treatment.

### Castling
Well, another [special move](https://en.wikipedia.org/wiki/Castling).
This one can be done on the kingside and on the queenside, but only if
the king is not in check, has not been moved, and if none of the passing fields are under
attack. This one can probably be implemented simply by setting a
bit.

## The Game Tree
In order to build the game tree, there needs to be a move-generating function.
This function needs to be fast, as all the moves correspond to nodes
in the tree. There is often even [special evaluation
code](https://www.chessprogramming.org/Perft) for the speed of the
generation of the movements.
On the tree, the [Minimax](https://en.wikipedia.org/wiki/Minimax)
Algorithm is used. This algorithm computes the next move with the
minimum maximal loss.
In our case, we give each move a number that corresponds to the
advantage of one side. Assuming optimal play from the opponent (the
maximal loss), we want to minimize that by choosing our move.
As chess is a zero-sum game (the advantage of one side is the
disadvantage of the other side), we can use
[Negamax](https://en.wikipedia.org/wiki/Negamax) which computes the
same result as Minimax, but is simpler to implement.
Of course, this can be enhanced by using various heuristics, such as 
[alpha-beta pruning](https://en.wikipedia.org/wiki/Alpha%E2%80%93beta_pruning) or the [Killer heuristic](https://en.wikipedia.org/wiki/Killer_heuristic).
As the full game tree cannot be computed, the evaluation of the
advantage is done in a certain depth of the game tree, such as six. In
that case, the engine would compute six moves ahead.

## Evaluation Function
At first it seems that the whole magic of the chess engine is in the
evaluation function. However, that is not completely true. The
depth of the evaluation also certainly plays an important role.
The most common [assignment of values to game
pieces](https://en.wikipedia.org/wiki/Chess_piece_relative_value) is
pawn=1, bishop=3, knight=3, rook=5, queen=9.
Note that the value additionally depends on the position of the piece.
Thus, the pieces can be weighted by being more in the center.

## The Horizon Effect
The [horizon effect](https://en.wikipedia.org/wiki/Horizon_effect)
occurs, if the search tree is only evaluated to a fixed depth. Attacks
beyond that depth (beyond the horizon) are not evaluated.
As an example, assume that the game tree is searched up to depth 10.
Then, if we can capture the queen after ten moves, this move is
evaluated high. However, it may be the case, that we lose our queen
certainly in move eleven. The chess engine will not be able to detect
this threat. Additionally, sometimes sacrifices of pieces can lead to
better positions.
A technique to mitigate this problem is called
[Quiescence](https://en.wikipedia.org/wiki/Quiescence_search) [Search](https://www.chessprogramming.org/Quiescence_Search).
Basically, in the end node, all paths consisting of immediate captures
are searched, until the node is 'quiet', and then the evaluation is
performed.

## Monte Carlo Tree Search
The [Monte Carlo Tree
Search](https://en.wikipedia.org/wiki/Monte_Carlo_tree_search) is a
separate approach to chess engines that does not require an evaluation
function. But first, a little bit of history.
Ten years ago, it was unthinkable that in the board game Go computers
will ever beat humans. It was thought that in order to play Go well,
a lot of pattern recognition and other algorithmic hurdles have to be
tackled. Go has an even larger game tree than chess, and thus even
partial search is futile. Well, then Google developed
[AlphaGo](https://en.wikipedia.org/wiki/AlphaGo), a computer program
that beat the Go grandmaster Lee Sedol.
The algorithmic improvements of AlphaGo were then used to play chess,
leading to [AlphaZero](https://en.wikipedia.org/wiki/AlphaZero).
AlphaZero then crushed the then strongest chess engine
[Stockfish](https://stockfishchess.org/).
As Stockfish is
[open-source](https://github.com/official-stockfish/Stockfish), the
guys from Stockfish thought that the algorithmic improvements from
AlphaZero should also be available in an open-source form. This lead
to the development of
[Leela](https://en.wikipedia.org/wiki/Leela_Chess_Zero)
[Chess](https://lczero.org)
[Zero](https://github.com/LeelaChessZero/lc0).

This new breed of chess engines does not use an evaluation function,
but only knows the rules of chess. It then trains an internal neural
network by playing against itself. I have not yet researched the
details, as I do not have enough money to train such a beast after
programming one, but what I understood is that it makes use of [Monte
Carlo Tree
Search](https://en.wikipedia.org/wiki/Monte_Carlo_tree_search).
In this algorithm, the game states are not evaluated by an evaluation
function, but rather by randomly playing a node to the end of the
game. The evaluation of that node is then the expected value of the
win.
Note that this gives only the 'average' score of nodes. In cases when
there are only few good moves, and most of the moves are bad, Monte
Carlo tree search may not find the good moves and may falsely infer
that the move is bad.

## Interfaces
In order for the engine to play, there needs to be an interface to the
real world. Luckily, there is the
[UCI protocol](http://wbec-ridderkerk.nl/html/UCIProtocol.html).
This protocol communicates over clear text and allows to play chess.
As an example, it is possible to hook up the engine via telnet to a
remote chess server such as
[FICS](https://en.wikipedia.org/wiki/Free_Internet_Chess_Server) and
let it play there.

Additionally there are multiple notations for chess moves and board
positions. These can be helpful in debugging.

For board positions, there is [Forsyth-Edwards
Notation](https://en.wikipedia.org/wiki/Forsyth%E2%80%93Edwards_Notation) and [Extended Position Description](https://www.chessprogramming.org/Extended_Position_Description).
As far as I know, this is only useful, when chess is started in other
positions as the default one. FEN and EPD are not really human-readable, and
for debugging, pretty printing the board with the usual signs (r=rook,
p=pawn, ...) seems easier to me.

There is [Portable Game
Notation](https://en.wikipedia.org/wiki/Portable_Game_Notation).
This can record chess games and seems to look very useful to be.
PGN can be converted to board positions by playing through the game.
After the metadata, the heart of PGN consists of the description of
the chess moves in [algebraic
notation](https://en.wikipedia.org/wiki/Algebraic_notation_(chess)).

## Opening and Ending
The opening and ending are often handled separately.

### Opening
In the opening, we are at the start of the tree and cannot really
decide between good and bad moves, as the tree has such an immense
depth. However, there are standard openings in human games. We may think
that humans have evolved chess through centuries of evolution and thus
these openings are good. In that case, we can give our chess engine a
book of openings which it can use. This is just a really long table on
what to do in the first five moves.

### Ending
In the ending, it may be the case that only three pieces are on the
board and that there is a wide variety of moves. However, computing
through all these moves may take a long time.
For this case, there are special [endgame
tables](https://syzygy-tables.info/). These can be used to see if the
current game is won, lost, or a draw.


## Conclusion
Most of the described parts are optional optimizations.
In general, only a board representation, a movement generating
function, as well as the search tree with its evaluation is needed.
Additionally, it may be of interest, that chess engines are not as
complicated as they once have been. DeepBlue was designed by
Masterminds using state of the art technology. Currently, a chess
engine is in the reach of a hobby programming project.
