---
title: Understand the Backend
---

## Overview

The "backend" for all games is compromised of four functions found in the highest level **index.js** file. Currently, for this file to work you cannot import any outside functions - all of your code must be in this file.

#### Objects

### BoardGame

```json
{
  "joinable": true,
  "finished": false,
  "players:" "[]",
  "version": 0,
  "state": {}
}
```

A JSON object provided to you that contains information about the current board game state.

# joinable

**boolean** Initially true, can be modified. 

Marks the game as either joinable or unjoinable. If true, new users will be able to join this game instance. If false, this game instance will not be included in the matchmaking queue and new players will be blocked from joining a private room with this game instance.

# finished

**boolean** Initially false, can be modified. 

Marks the game as finished or in-progress. If true, the game is unjoinable, no new changes can be made to the board game state, and the game instance will show in the "Played Games" list for all present players. If false, the game will show in the "Active Games" list for all present players.

# players

**[string]** Initially empty, cannot be modified. Will update as players join and leave the game instance. 

A list of the playerIds of all the players in the game.

# version

**int** Initially 0, cannot be modified.

The current version of the board game state - is incremented with every change. Is used to keep all players in sync with the current board game state.

# state

**object** Initially empty, can be modified to any configuration.

The board game state of your specific game - can hold any information, and is only used internally in your game logic.

### BoardGameResult

```json
{
  "joinable": false,
  "finished": false,
  "state": {}
}
```

A JSON object that your functions will return - contains the aspects of the BoardGame that can be modified. Will be used to update your BoardGame object.

#### Functions

### onRoomStart

```ts
onRoomStart = () => BoardGameResult
```

Runs when the room is first initialized (i.e. when a private room is created or a room is created for the matchmaking queue). Must return the BoardGameResult [link].

Use this function to initialize your board game state. [Tic Tac Toe Example]

### onPlayerJoin

```ts
onPlayerJoin = (player: string, boardGame: object) => BoardGameResult
```

Runs when a player joins the room, reveals the id of the player who joined and the current BoardGame. Must return the BoardGameResult [link].

[TicTacToe Example]

### onPlayerQuit

```ts
onPlayerQuit = (player: string, boardGame: object) => BoardGameResult
```

Runs when a player quits the game (i.e. they press the back button, close the browser, close the tab, etc.). Reveals the id of the player who quit and the current BoardGame. Must return the BoardGameResult [link].

Use this function to handle a player a quitting the game early - for example, in TicTacToe where once a game has started, if one of the player leaves they forfeit the game. [TicTacToe example]. ```onPlayerMove``` is handles the winning game move, ```onPlayerQuit``` handles a player leaving the game entirely.

DEFINE QUITTING AND LINK IN IMPLEMENTING LOGIC

### onPlayerMove

```ts
onPlayerMove = (player: string, move: object, boardGame: object) => BoardGameResult
```

Runs when a player moves (i.e. when ```client.makeMove()``` is called). Reveals the id of the player that made the move, the object containing the move, and the current BoardGame. The move object is defined by you and can be any JSON object. Must return the BoardGameResult. [link]

[TicTacToe Example]
