
# Chess Engine




## Features

- User can play with Engine
- Can parse the **FEN** and set the board
- Can find best move in a given position
- Searches on average 1299866 position and depth 7 


## Documentation

### 3 major component
- Board Representation
- Move Generator
- Search Move




## Board Representation


chess board is generally a ``8*8`` board with files representing coloumn `a-h`
and rank represents rows 1-8
So each cell is called like `a1 , h8  etc`
	
|  | a | b | c | d | e | f | g | h |
|---|---|---|---|---|---|---|---|---|
|8 | A8 | B8 | C8 | D7 | E8 | F8 | G8 | h8 |
|7 | A7 | B7 | C7 |	D6 | E7 | F7 |	G7 | H7 |
|6 |A6 |B6 | C6 | D5 | E6 | F6 | G6 | H6 |
|5|	A5|	B5|	C5|	D4|	E5|	F5|	G5|	H5|
|4|	A4|	B4|	C4|	D3|	E4|	F4|	G4|	H4|
|3|	A3|	B3|	C3|	D2|	E3|	F3|	G3|	H3|
|2|	A2|	B2|	C2|	D2|	E2|	F2|	G2|	H2|
|1|	A1|	B1|	C1|	D1|	E1|	F1|	G1|	H1|

 
**My implementation** 

1d array of 120 length 

|0|	1|	2|	3|	4|	5|	6|	7|	8|	9|
|---|---|---|---|---|---|---|---|---|---|
|10|11|	12|	13|	14|	15|	16|	17|	18|	19|
|20|21|	22|	23|	24|	25|	26|	27|	28|	29|
|30|31|	32|	33|	34|	35|	36|	37|	38|	39|
|40|41|	42|	43|	44|	45|	46|	47|	48|	49|
|50|51|	52|	53|	54|	55|	56|	57|	58|	59|
|60|61|	62|	63|	64|	65|	66|	67|	68|	69|
|70|71|	72|	73|	74|	75|	76|	77|	78|	79|
|80|81|	82|	83|	84|	85|	86|	87|	88|	89|
|90|91|	92|	93|	94|	95|	96|	97|	98|	99|
|100|101|102|103|104|105|106|107|108|109|
|110|111|112|113|114|115|116|117|118|119|


here each value represent `index` of board array

Actual Board is from index `21 to 98`

```
// some varible for reference
var SQUARES = {
  A1:21, B1:22, C1:23, D1:24, E1:25, F1:26, G1:27, H1:28,  
  A8:91, B8:92, C8:93, D8:94, E8:95, F8:96, G8:97, H8:98, 
  NO_SQ:99, OFFBOARD:100
};
```
**Peice Representation**
```
var PIECES =  { EMPTY : 0, wP : 1, wN : 2, wB : 3,wR : 4, wQ : 5, wK : 6, 
              bP : 7, bN : 8, bB : 9, bR : 10, bQ : 11, bK : 12  };


// board[idx] = 0 means sq is EMPTY
// board[idx] = 1 means white pawn on that idx 
```

**fileRank_to_Square**

sq = (f + 21) + r*10 


## Move Generator

**Generation** of moves is a basic part of a chess engine with many variations concerning a generator
 or an iterator to loop over moves inside the search routine.
 The implementation heavily depends on the board representation, 
 and it can be generalized into two types, pseudo-legal and legal move generation.
 
 #### Legality check

 1. pseudo legal Move
 2. Legal Move


*pseudo Legal move*

    In Pseudo-legal move generation pieces obey their normal rules of movement,
    but they're not checked beforehand to see if they'll leave the king in check.

*Legal Move*

    only legal moves are generated, which means we need to make sure the king isn't going to be  in check after making move
    
## Search Move

Finding or guessing a good move in a chess position is hard to achieve statically, chess programs rely on some type of Search in order to play reasonably. Searching involves looking ahead at different move sequences and evaluating the positions after making the moves.

It consist of 2 part

1. recursive function for Searching move
2. Evaualte the given Position.



```
// pseudo recursive code

function Search(int depth,int side){
    if(depth == 0)return Evaualte()
    GenerateMove();
    var score;
    for(var i = 0;i<MoveList.length();i++){// iterating in moves
        makeMove();
        if(side == oponent){
            score = min(score);
        }else{
            score = max(score);
        }
        takeback();
    }
    return score;


}


```


**Alpha Beta Algorithm**

The Alpha-Beta algorithm is a significant enhancement to the normal dfs or minimax search algorithm that eliminates the need to search large portions of the game tree applying a branch-and-bound technique. 
It works like this , If one already has found a quite good move and search for alternatives, one refutation is enough to avoid it. No need to look for even stronger refutations.
 The algorithm maintains two values, alpha and beta.

 **Reordering of Moves**
 For the alpha-beta algorithm to perform well, the best moves need to be searched first. This is especially true for PV-nodes and expected Cut-nodes. The goal is to become close to the minimal tree. On the other hand - at Cut-nodes - the best move is not always the cheapest refutation, see for instance enhanced transposition cutoff. Most important inside an iterative deepening framework is to try the principal variation of the previous iteration as the leftmost path for the next iteration, which might be applied by an explicit triangular PV-table or implicit by the transposition table.

**Quiescence Search**

The main idea of Quiescence search is that to stop the search when no *capture move* is possible.

EX-Consider the situation where the last move you consider is QxP. If you stop there and evaluate, you might think that you have won a pawn. But what if you were to search one move deeper and find that the next move is PxQ? You didn't win a pawn, you actually lost a queen. Hence the need to make sure that you are evaluating only quiescent (quiet) positions.


```


int Quiesce( int alpha, int beta ) {
    int stand_pat = Evaluate();
    if( stand_pat >= beta )
        return beta;
    if( alpha < stand_pat )
        alpha = stand_pat;

    until( every_capture_has_been_examined )  {
        MakeCapture();
        score = -Quiesce( -beta, -alpha );
        TakeBackMove();

        if( score >= beta )
            return beta;
        if( score > alpha )
           alpha = score;
    }
    return alpha;
}

```
