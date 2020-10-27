# Introduction

The objective of this test it is to build a functional tic-tac-toe. It is a traditional game where players have to place three marks of the same color in a row.

![](http://www.gifmania.co.uk/Objects-Animated-Gifs/Animated-Toys/Board-Games/Tic-Tac-Toe/Neon-Tic-Tac-Toe-89376.gif)

You can play it visiting [this link](https://www.google.com/search?q=tic+tac+toe) of Google results.

More requirements you complete, more points you will get. Evaluation of requirements completion will be executed both automatically and manually.

# Backend requeriments

Following rules are strictly mandatory for the evaluation of the test:

* Technologies: ([Node.js](https://nodejs.org/) with [Express.js](https://expressjs.com/), [Koa](http://koajs.com/) or [Fastify](https://www.fastify.io/))

* Build one POST web service listening to requests on `/api/tic-tac-toe/play` that matches following specification.

## Request body

Public web service should accept body param with a structure similar to this one:

```
{
  "matchId": "5a5c6f9b4d749d008e07e695", //string, identifies the current match, required
  "boardState": [
    "x", "-", "-", // first row of the game board, positions 0, 1, 2
    "-", "o", "o", // second row of the game board, positions 3, 4, 5
    "-", "-", "x" // third row of the game board, positions 6, 7, 8
  ], // array of chars ( one of ['o','x','-']), required
  "nextMove": {
    "char": "o", // char one of ['o','x'], required
    "position": 4 // number from 0 to 8, required 
  }, // object, represents the next move of the player, required only on input
  "history": [
    {
      "char": "x",
      "position": 0
    }
  ], // array of move objects, optional
  "customField1": "customValue1", // any format, optional for the developer
  "customField2": "customValue1", // any format, optional for the developer
  "customField3": "customValue1" // any format, optional for the developer
}
```

This is the human specification of the previous payload:

* `matchId`: Mandatory string that identifies the current match.
* `boardState`: Array of strings that contain the state of the board. It has a size of 3 x 3 row and each element has one of the following values: `x`, `-` or `o`
* `nextMove`: Mandatory object that contains information of the next move. It contains two mandatory fields that indicates the type of mark you want to add (valid values: `x` and `o`) and another one that indicates the position where you want to put it.
* `history`: Mandatory list of previous moves sorted by time (ascendent). The structure of the items it contains is the same than `nextMove` field.

Additionally, you can add three additional fields to store any kind of information you consider relevant for this challenge.

## Response body

Server should return the same structure than request but updated with the move provided by user and another move done by a bot. Field `nextMove` must be set to null because both moves were already applied.

## Business rules

Backend should verify user has not cheated, play his turn and return the updated board.

### Game initialisation

Any request without payload will start a new match. When starting a new match the server can play first or not randomly.

If machine plays first, mark type (`x` or `y`) will be assigned randomly. If user moves first, he can choose mark type.

### Request validation

Server should verify following rules:

* Request payload is a valid JSON.
* Provided `nextMove` is valid.
* Provided `matchId` was not modified.
* Match did not finish.

In case any of those rules are not satisfied, backend should return a message with following structure:

```
{
  "error": true,
  "message": "Error message"
}
```

These are the expected error messages (keep them literal for testing):
* _Not valid payload format_
* _Not valid move_
* _Not valid matchId_
* _Match has finished_

In case you need any other error, just add it and document new error cases in your README file.

### Bot moves

If move is valid, server has to calculate new board state adding both human and bot move.

Algorithm for machine moves has to be implemented by you as smart as possible. If you add documentation or diagrams to explain it, it will be appreciated.

### Game example

This is an example of two moves just to explain with a practical example how backend should work.

#### First request

On first request, we send an empty body to backend.

```
{}
```

In this case random start says backend has to play first, so it responds with his first movement after a random choice of mark type.

```
{
  matchId: "5a5c6f980bc11e007432ca3f",
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

#### Second request

Human player thinks about next move and frontend sends his move to backend.

```
{
  "matchId": "5a5c6f980bc11e007432ca3f",
  "boardState": [
    "-", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ],
  "nextMove": { "char": "x", "position": 0 },
  "history": [
    { "char": "o", "position": 4 }
  ]
}
```

Backend receives his move, updates the board and add his own move before it sends the response. As you can see, `nextMove` does not contain data because his move was already added to the board.

```
{
  "matchId": "5a5c6f980bc11e007432ca3f",
  "boardState": [
    "x", "-", "-",
    "-", "o", "-",
    "-", "-", "-"
  ],
  "history": [ 
    { "char": "o", "position": 4 },
    { "char": "x", "position": 0 } 
  ]
}
```

# Frontend Requirements

This part has to be made with [React](https://reactjs.org/) and it should allow user to play a complete match versus the CPU using the previous web service and clicking on the board.

More requirements you complete, more points you will get on test evaluation.

# Scoring

## Mandatory

- The server detects not valid ids (1 points)
- The server detect not valid payloads (2 points)
- The CPU player plays correctly (2 points)
- The server maintains the complete list of past moves on the history (2 points)
- The UI don't allow wrong moves (2 points)
- The UI allows to play a complete match without noticeable problems (3 points)
- Clean and readable code (3 points)
- The server detect not valid moves correctly (3 points)
- Functional oriented code [no stateful objects are being defined] (4 points)

## Select 2 of the next 4

- Tested code [worthy unit / functional tests] (4 points)
- The server allows to undo the last move multiple times [use `{...payload, nextMove: { undo: true }}`] (4 points)
- The CPU player should be able to tie or win always (6 points)
- The server is able to maintain a valid flow of successive boardStates without using any kind of storage [db, file, memory] (6 points)

## Total points: 30 - 34
