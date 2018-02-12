# CheckerboardProject
## Checkers Model
The ultimate goal is to create a checkers game. This document is an outline for an idea to develop this game in Java.

### Checkerboard Class
In our previous challenge we created a class called Checkerboard that would be used to render the board in the view. The constructor looks like this:
```Java
public Checkerboard(int rows, int columns, double boardWidth, double boardHeight){
        this.rows = rows;
        this.columns = columns;
        this.boardWidth = boardWidth;
        this.boardHeight = boardHeight;
    }
```

### Player class
There are three scenarios for the checkers game: Human v Human, Human v Computer, and Computer v Computer.
This is set when the Player class is instatiated:
```Java
boolean isHuman;
boolean isWinner;
String playerNumber;

public Player(boolean isHuman, String playerNumber){
  this.isHuman = isHuman;
  this.playerNumber = playerNumber;
}
```
#### Checker Types
There are 4 different states that checker pieces can be in, and this will be displayed in an enumeration:

```Java
enum PieceType{ BLACK_KING, BLACK_REGULAR, RED_KING, RED_REGULAR }
```
### CheckerPiece Class
There will be a class called CheckerPiece that will be used to identify each checker piece, their place on the board, their status (King, regular), and which player they are for (Red or Black). The constructor is like this:

```Java
int row;
int column;
enum pieceType;

// Constructor
public CheckerPiece(int startingRow, int startingColumn, PieceType pieceType){
  row = startingRow;
  column = startingColumn;
  this.pieceType = pieceType;
}
```
The CheckerPiece class will have public methods to get the pieceType and row/column indexes. This class will also have methods to set the row and column indexes after a player moves the piece and it is a legal move.

#### Moving Pieces
The pieces will have their spaces identified on the board through (row, column) indexing. The top left corner will be indexed as (1,1), the top right as (1,8), the bottom left as (8,1), and the bottom right as (8,8).

In order for move to be legal for a regular checker piece, the first index of the new position must be one greater than the original first index. The second index can be plus or minus the second index of the original position, but never below or above the values of 1 and 8 respectively.

### CheckerSpace Class
These values will be stored in a CheckerSpace class. There will be two constructors for this Class while rendering the board. One class will set a boolean variable isOccupied as true if a Checker object is sent and false if there only row and column are sent.

```Java
public CheckerSpace(int row, int column){
  this.row = row;
  this.column = column;
  isOccupied = false;
}

public CheckerSpace(int row, int column, Checker checkerpiece){
  this.row = row;
  this.column = column;
  isOccupied = true;
}
```
While building the board, a hashMap will hold the CheckerSpace objects created. There will be 64 elements (8x8), and a for-loop will iterate to fill all of the spots with new CheckerSpace objects.
The Key is a class that we define that holds the row and column values, and the value passed is the CheckerSpace object.
```Java
public Map<Key, CheckerSpace> checkerSpaceMap = new HashMap();
```
```Java
public class Key {

    private final int x;
    private final int y;

    public Key(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Key)) return false;
        Key key = (Key) o;
        return x == key.x && y == key.y;
    }

    @Override
    public int hashCode() {
        int result = x;
        result = 31 * result + y;
        return result;
    }

}
```
The CheckerSpace class will contain methods to determine legal moves of that position and whether or not a piece is occupying the space.
```Java
boolean public isLegal(int newRow, int newColumn, Checker checkerPiece){
  //If the player is trying to move back a row and they are not a King, the method will return false
  if(newRow < row && checkerPiece.getType() != (BLACK_KING || RED_KING)){
    return false;
  }
  // The new row can't be greater than 8 or less than 1
  if(newRow > 8 || newRow < 1){
    return false;
  }
  else if (newColumn <= 0 || newColumn > 8){
    return false;
  }
  else if(newRow + 1 > 8 || newRow - 1 < 1){
    return false;
  }
  else if(checkerSpaceMap.get(new Key(row,column)).isOccupied()){
    //more methods to check if the space beyond it is occupied and we can take the piece.

    //.isOccupied() would be a method in the CheckerSpace class to see if the space is occupied i.e. the variable isOccupied is a boolean true or false.
  }
  else return true;
}
```

### Making Moves
The winner of the game is determined by all of the pieces being taken by an opponent, or there being no more moves to make.
In the CheckerSpace class, static variables called redCount and blackCount will keep track of the number of red and black checker pieces on the board. If a piece is captured, a static method will be called to decrement the total count of a red or black piece.

```Java
public static int redCount = 12;
public static int blackCount = 12;

public static void pieceCaptured(PieceType playerCaptured){
  if(playerCaptured.equals(BLACK_KING) || playerCaptured.equals(BLACK_REGULAR){
    blackCount--;
  }
  else
    redCount--;
}

public static int checkPieceCounts(){
  if(redCount == 0){
    return 1;
  }
  else if (blackCount == 0){
    return 2;
  }
  else 0; //return 0 if neither have reached 0 pieces.
}
```
#### Check if a Draw

As we just saw a player can be determined a winner if the red or black checker piece count reaches zero.
Another way the  game can end is through draw. The draw would be determined by a method called checkIfADraw(). This method checks if all of the pieces return false when isLegal is called. If all of the methods return false, then the game is a draw.
```Java
  private boolean checkIfADraw(){

    for(Map.Entry<Key, CheckerPiece> entry : checkerSpaceMap.entrySet()) {
      Key key = entry.getKey();
      Checker checkerPiece = entry.getValue();
      int row = key.getRow();
      int column = key.getColumn(); //getters in our key Class

      boolean isItLegal = isLegal(row, column, checkerPiece);

      return isItLegal;
    }
  }
```
