# Introduction (frontend version)

The objective of this test it is to build a functional tic-tac-toe. It is a traditional game where players have to place three marks of the same color in a row.

![](http://www.gifmania.co.uk/Objects-Animated-Gifs/Animated-Toys/Board-Games/Tic-Tac-Toe/Neon-Tic-Tac-Toe-89376.gif)

You can play it visiting [this link](https://www.google.com/search?q=tic+tac+toe) of Google results.

More requirements you complete, more points you will get.

# State requirements


## State structure

The state structure should be similar to the next one:

```
{
  "matchId": "5a5c6f9b4d749d008e07e695", //string, identifies the current match, required
  "boardState": [
    "x", "-", "-", // first row of the game board, positions 0, 1, 2
    "-", "o", "o", // second row of the game board, positions 3, 4, 5
    "-", "-", "x" // third row of the game board, positions 6, 7, 8
  ], // array of chars ( one of ['o','x','-']), required
  "lastMove": {
    "char": "o", // char one of ['o','x'], required
    "position": 4 // number from 0 to 8, required 
  }, // object, represents the next move of the player, required only on input
  "customField1": "customValue1", // any format, optional for the developer
  "customField2": "customValue1", // any format, optional for the developer
  "customField3": "customValue1" // any format, optional for the developer
}
```

This is the human specification of the previous payload:

* `matchId`: Mandatory string that identifies the current match.
* `boardState`: Array or Object of strings that contain the state of the board. It should include 3 x 3 elements (row x columns) and each element has one of the following values: `x`, `-` or `o`
* `lastMove`: Mandatory object that contains information of the last move. It contains two mandatory fields that indicates the type of mark you want to add (valid values: `x` and `o`) and another one that indicates the position where the player played before.


Additionally, you can add three additional fields to store any kind of information you consider relevant for this challenge.

### Game initialisation

When starting a new match the CPU can play first or not randomly.

If CPU plays first, mark type (`x` or `y`) will be assigned randomly. If user moves first, he can choose mark type.

### Bot moves

If move is valid, server has to calculate new board state adding both human and bot move.

Algorithm for machine moves has to be implemented by you as smart as possible. If you add documentation or diagrams to explain it, it will be appreciated.

### Game example

This is an example of two moves just to explain with a practical example how the game should work.

#### First move

Before the first move, we have an empty state structure similar to this.

```
{
  matchId: "5a5c6f980bc11e007432ca3f",
  boardState: [
    "-", "-", "-",
    "-", "-", "-",
    "-", "-", "-"
  ]
}
```

In this case random start says backend has to play first, so it responds with his first movement after a random choice of mark type.

```
{
  matchId: "5a5c6f980bc11e007432ca3f",
  boardState: [
    "-", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ]
}
```

#### Second move

Human player thinks about next move and adds his move by clicking on the board.

```
{
  "matchId": "5a5c6f980bc11e007432ca3f",
  "boardState": [
    "-", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ],
  "lastMove": { "char": "x", "position": 0 }
}
```

The state receives his move, updates the board and add his own move with a random delay time of 1 - 3 secs.
```
{
  "matchId": "5a5c6f980bc11e007432ca3f",
  "boardState": [
    "x", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ]
}
```

# Frontend Requirements

This part has to be made with [React](https://reactjs.org/) and it should allow user to play a complete match versus the CPU using the previous web service and clicking on the board.

Usage of redux or react hooks for the state management are mandatory.

More requirements you complete, more points you will get on test evaluation.

# Scoring

## Mandatory

- Clean and readable code (3 points)
- The UI prevents invalid moves (2 points)
- Functional oriented code (3 points)
- Proper CSS styling (3 points)
- Last player move can be undone / reverted (4 points)
- The CPU player should be able to tie or win always (5 points)
- Only changed board elements are rendered (6 points)
