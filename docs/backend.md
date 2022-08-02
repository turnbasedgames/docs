---
title: Understanding the Backend
---

## Overview

The "backend" for all games is compromised of four functions found in the highest level **index.js** file. Currently, for this file to work you cannot import any outside functions - all of your code must be in this file.

## Objects

### BoardGame

```json
{
  "joinable": true,
  "finished": false,
  "players": "[]",
  "version": 0,
  "state": {}
}
```

A JSON object provided to you that contains information about the current board game state.

#### joinable: *boolean*

Initially true, can be modified. 

If true, new users will be able to join this game instance. If false, this game instance will not be included in the matchmaking queue and new players will be blocked from joining a private room with this game instance.

#### finished: *boolean*

Initially false, can be modified. 

If true, no new changes can be made to the board game state and the game instance will show in the "Played Games" list for all present players. If false, the game will show in the "Active Games" list for players.

#### players: *[ string ]*

Initially empty, cannot be modified.

A list of the IDs of all the players in the game. Will update as players join and leave the game instance. 

#### version: *int*

Initially 0, cannot be modified.

The current version of the board game state. Incremented with every change. Is used to keep all players in sync with the current board game state.

#### state: *JSON object*

Initially empty, can be modified to any configuration.

Can hold any valid JSON object and is only used internally in your game logic.

### BoardGameResult

```json
{
  "joinable": false,
  "finished": false,
  "state": {}
}
```

A JSON object that your functions can return - contains the aspects of the BoardGame that have been modified. Will be used to update your [BoardGame](#boardgame) object.

## Functions

### onRoomStart

```ts
onRoomStart = () => BoardGameResult
```

Runs when the room is first initialized, as triggered by these actions:
1. When a private room is created (player clicks *Create Private Room*)
2. When a room is created for the matchmaking queue (player clicks *Play*)

Returns the [BoardGameResult](#boardgameresult). Use this function to initialize your board game state.

### onPlayerJoin

```ts
onPlayerJoin = (player: string, boardGame: object) => BoardGameResult
```

Runs when a player joins the room. Reveals the ID of the player who joined and the current [BoardGame](#boardgame) state. Returns the [BoardGameResult](#boardgameresult).

### onPlayerQuit

```ts
onPlayerQuit = (player: string, boardGame: object) => BoardGameResult
```

Runs when a player quits the game. A player **only** quits the game by manually clicking the ***quit*** button - closing the browser or tab will not end the game session. Reveals the ID of the player who quit and the current [BoardGame](#boardgame) state. Returns the [BoardGameResult](#boardgameresult).

### onPlayerMove

```ts
onPlayerMove = (player: string, move: object, boardGame: object) => BoardGameResult
```

Runs when a player moves (i.e. when ```client.makeMove()``` is called). Reveals the ID of the player that made the move, the object containing the move, and the current [BoardGame](#boardgame) state. The move object is defined by you and can be any JSON object. Returns the [BoardGameResult](#boardgameresult).
