# JS tick-tack-toe

The objective of the test it's to build a functional tick-tack-toe 

Sample Game: https://www.google.es/search?q=tick+tack+toe&oq=tick+tack+toe

## Server Requirements

- Technologies (NodeJS with express, koa or meteor)

- Build one POST webservice /api/tick-tack-toe/play that receives as input the current status of the game and the next move of the human player:

example of a valid input JSON body:
```js
{
  matchId: "123", //string, identifies the current match, required
  
  boardState: [
    "x", "-", "-", // first row of the game board, positions 0, 1, 2
    "-", "o", "o", // second row of the game board, positions 3, 4, 5
    "-", "-", "x"  // third row of the game board, positions 6, 7, 8
  ], // array of chars ( one of ['o','x','-']), required
  
  nextMove: {
    char: "o" // char one of ['o','x'], required
    position: 1 // number from 0 to 8, required 
  }, // object, represents the next move of the player, required only on input
  
  history: [ { char: "x", position: 0 } ], // array of move objects, optional
  
  customField1: // any format, optional for the developer
  customField2: // any format, optional for the developer
  customField3: // any format, optional for the developer
  }
```

- The server should check if the movement it's valid, if the the payload is wrongly formatted, if the matchId it's valid if any of these conditions fails should respond with and error like these (status code 400):
```js
{
  error: true,
  message: "Not valid move"
  // other possible message values
  // message: "Not valid payload format"
  // message: "Not valid matchId"
  // message: "Match has finished"
}
```

- If the movement it's valid the server calculates the next boardState and applies a valid CPU movement and returns the result maintaining the same JSON scheme as the input payload.

- Any request to the webservice without payload will create / start a new match, when starting a new match the server can act first (doing a CPU move) or not randomly, the CPU char "x" or "o" get assign randomly too. When the CPU don't act first the Player can select any char.


## Frontend Requirements


- Technologies (React)
- The user should be able to play a complete match VS the CPU using the webservice defined above.
- The user should be able to play by clicking on the board

## Example of a correct flow:

### 1st REQUEST - Game starts and CPU plays first

#### INPUT

empty BODY

#### OUTPUT

```js
{
  matchId: "1",
  boardState: [
    "-", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ],
  history: [ 
    { char: "o", position: 4 } 
  ]
}
```

### 2nd REQUEST - Human player send a correct move and server responds with corresponding CPU move

#### INPUT

```js
{
  matchId: "1",
  boardState: [
    "-", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ],
  nextMove: { char: "x", position: 0 },
  history: [ 
    { char: "o", position: 4 } 
  ]
}
```

#### OUTPUT

```js
{
  matchId: "1",
  boardState: [
    "x", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ],
  history: [ 
    { char: "o", position: 4 }, { char: "x", position: 0 } 
  ]
}
```

## Scoring

### Mandatory

- The server detects not valid ids (1 points)
- The server detect not valid payloads (2 points)
- The CPU player plays correctly (2 points)
- The server maintains the complete list of past moves on the history (2 points)
- The UI don't allow wrong moves (2 points)
- The UI allows to play a complete match without noticeable problems (3 points)
- Clean and readable code (3 points)
- The server detect not valid moves correctly (3 points)
- Functional oriented code [no stateful objects are being defined] (4 points)

### Select 2 of the next 4

- Tested code [worthy unit / functional tests] (4 points)
- The server allows to undo the last move multiple times [use `{...payload, nextMove: { undo: true }}`] (4 points)
- The CPU player should be able to tie or win always (6 points)
- The server is able to maintain a valid flow of successive boardStates without using any kind of storage [db, file, memory] (6 points)

#### Total points: 30 - 34
