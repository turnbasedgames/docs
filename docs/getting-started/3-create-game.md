---
title: Creating Your First Game!
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Overview

We are ready to make our first game - tic-tac-toe! There are two major components of your game: the [frontend](#frontend) and the [backend](#backend). We will go over the basics of each.

## The Backend

### What is Board Game State?

Your game state is held in the [BoardGame](/docs/backend#boardgame) object. You can tell UrTurn if your game is joinable and/or if it is finished. You can also define the "state" object that will define the way the board currently looks. For this tic-tac-toe game, the BoardGame state will look like this:

```json
{
  "players": "[]", // controlled by UrTurn
  "version": 0, // controlled by UrTurn
  "joinable": true,
  "finished": false,
  "state": {
    "board": "[
      [null, null, null],
      [null, null, null],
      [null, null, null],
    ]",
    "winner": null
  },
}
```

We will be manipulating the ```joinable```, ```finished```, and ```state``` properties of this object to control our game.

### Four Functions - That's It!

All of our game logic can be encompassed by the following four functions:

#### 1. onRoomStart

This function will be called whenever a [room is created](/docs/backend#onroomstart). When the game starts, we want to initialize our empty board game state, which includes the following for tic-tac-toe:

1. The Board: A 3x3 square, initialized with null values.

2. The Winner: The winner's ID, if there is a winner. Initially null.

```js title="index.js"
function onRoomStart() {
  return {
    state: {
      status: "preGame",
      board: [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ],
      winner: null
    },
  };
}
```

#### 2. onPlayerJoin

This function will be called whenever a player actually joins the game. It provides us with the ID of the player who joined as well as the current [BoardGame state](/docs/backend#boardgame).

If this is the first player to join, we will just return an empty object as there are no changes to the game state. If this is the second player to join, then the game has all the necessary players and should be marked as unjoinable.

```js title="index.js"
function onPlayerJoin(plr, boardGame) {
  const { players, state } = boardGame;

  if (players.length === 2) {
    return { joinable: false };
  }
  return { };
}
```

#### 3. onPlayerQuit

This function will be called whenever a player [quits the game](/docs/backend#onplayerquit). It provides us with the ID of the player who quit and the current board game state.

For tic-tac-toe, the game will end if one of the players quits. The game will be marked as unjoinable and finished, and the remaining player will be marked the winner.

```js title="index.js"
function onPlayerQuit(plr, boardGame) {
  const { state, players } = boardGame;
  state.status = "endGame";

  if (players.length === 1) {
    const [winner] = players;
    state.winner = winner;
    return { state, joinable: false, finished: true };
  }
  return { joinable: false, finished: true };
}
```

#### 4. onPlayerMove

This function will be called whenever a player makes a move. It provides us with the ID of the player who made the move, the move object, and the current board game state. We can define the move object as any valid JSON object - for tic-tac-toe, it will be an object containing the x- and y-coordinates of the square they selected.

After the move is completed, if we determine the game is over and there is a winner, we will add the winner's ID to our state so it can be displayed on the frontend.

<Tabs>
<TabItem value="snippet" label="Snippet">

```js
function onPlayerMove(plr, move, boardGame) {
  const { state, players } = boardGame;
  const { board, plrToMoveIndex } = state;

  const { x, y } = move;

  const plrMark = getPlrMark(plr, players);

  board[x][y] = plrMark;

  const [isEnd, winner] = isEndGame(board, players);

  if (isEnd) {
    state.winner = winner;

    return { state, finished: true };
  }

  return { state };
}
```

</TabItem>
<TabItem value="full" label="Full Code">

```js
function getPlrMark(plr, plrs) {
  if (plr === plrs[0]) {
    return 'X';
  }
  return 'O';
}

function isEndGame(board, plrs) {
  function getPlrFromMark(mark, plrs) {
  return mark === 'X' ? plrs[0] : plrs[1];
}

  function isWinningSequence(arr) {
  return arr[0] != null && arr[0] === arr[1] && arr[1] === arr[2];
}

  // check rows and cols
  for (let i = 0; i < board.length; i += 1) {
    const row = board[i];
    const col = [board[0][i], board[1][i], board[2][i]];

    if (isWinningSequence(row)) {
      return [true, getPlrFromMark(row[0], plrs)];
    } if (isWinningSequence(col)) {
      return [true, getPlrFromMark(col[0], plrs)];
    }
  }

  // check diagonals
  const d1 = [board[0][0], board[1][1], board[2][2]];
  const d2 = [board[0][2], board[1][1], board[2][0]];
  if (isWinningSequence(d1)) {
    return [true, getPlrFromMark(d1[0], plrs)];
  } if (isWinningSequence(d2)) {
    return [true, getPlrFromMark(d2[0], plrs)];
  }

  // check for tie
  if (board.some((row) => row.some((mark) => mark === null))) {
    return [false, null];
  }
  return [true, null];
}

function onPlayerMove(plr, move, boardGame) {
  const { state, players } = boardGame;
  const { board, plrToMoveIndex } = state;

  const { x, y } = move;

  const plrMark = getPlrMark(plr, players);

  board[x][y] = plrMark;

  const [isEnd, winner] = isEndGame(board, players);

  if (isEnd) {
    state.winner = winner;

    return { state, finished: true };
  }

  return { state };
}
```

</TabItem>
</Tabs>

## Frontend

This section will go over how to implement the frontend for our tic-tac-toe so that it is visible to the user. We will be adding our components to ```frontend/src/App.jsx```. This file already contains some logic for you to access the [BoardGame](/docs/backend#boardgame) object and for any state changes to make to be propagated to your backend.

### 1. Extract the Board Game State

We will first extract the information we need from the board game state:

<Tabs>
<TabItem value="snippet" label="Snippet">

```jsx title="frontend/src/App.jsx"
const {
    state: {
      board,
      winner,
    } = {
      board: [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ],
    },
    players = [],
    finished
  } = boardGame;
```

</TabItem>
<TabItem value="full" label="Full Code">

```js
import React, { useState, useEffect } from 'react';
import { ThemeProvider, Typography } from '@mui/material';

import client, { events } from '@urturn/client';
import theme from './theme';

function App() {
  const [boardGame, setBoardGame] = useState(client.getBoardGame() || {});
  useEffect(() => {
    const onStateChanged = (newBoardGame) => {
      setBoardGame(newBoardGame);
    };
    events.on('stateChanged', onStateChanged);
    return () => {
      events.off('stateChanged', onStateChanged);
    };
  }, []);

  console.log('boardGame:', boardGame);

  const {
    state: {
      board,
      winner,
    } = {
      board: [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ],
    },
    players = [],
    finished
  } = boardGame;

  return (
    <ThemeProvider theme={theme}>
      <Typography>
        TODO: Display your game here
      </Typography>
    </ThemeProvider>
  );
}

export default App;
```

</TabItem>
</Tabs>

### 2. Create a Tic-Tac-Toe Board

Using our empty board game, we can render a simple tic-tac-toe board:

<Tabs>
<TabItem value="snippet" label="Snippet">

```jsx live
function App(props) {
  return (
   <ThemeProvider theme={theme}>
      <Typography>
        <Stack margin={2} spacing={1} direction="row" justifyContent="center">
          <Box>
            {board.map((row, rowNum) => (
              <Stack key={rowNum} direction="row">
                {row.map((val, colNum) => (
                  <Stack
                    key={colNum}
                    direction="row"
                    justifyContent="center"
                    alignItems="center"
                    sx={{
                      border: 1,
                      borderColor: 'text.primary',
                      height: '100px',
                      width: '100px',
                    }}
                  />
                ))}
              </Stack>
            ))}
          </Box>
        </Stack>
      </Typography>
    </ThemeProvider>
  );
}
```

</TabItem>
<TabItem value="full" label="Full Code">

```js
import React, { useState, useEffect } from 'react';
import { ThemeProvider, Typography } from '@mui/material';

import client, { events } from '@urturn/client';
import theme from './theme';

function App() {
  const [boardGame, setBoardGame] = useState(client.getBoardGame() || {});
  useEffect(() => {
    const onStateChanged = (newBoardGame) => {
      setBoardGame(newBoardGame);
    };
    events.on('stateChanged', onStateChanged);
    return () => {
      events.off('stateChanged', onStateChanged);
    };
  }, []);

  console.log('boardGame:', boardGame);

  const {
    state: {
      board,
      winner,
    } = {
      board: [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ],
    },
    players = [],
    finished
  } = boardGame;

  return (
    <ThemeProvider theme={theme}>
      <Typography>
        <Stack margin={2} spacing={1} direction="row" justifyContent="center">
          <Box>
            {board.map((row, rowNum) => (
              <Stack key={rowNum} direction="row">
                {row.map((val, colNum) => (
                  <Stack
                    key={colNum}
                    direction="row"
                    justifyContent="center"
                    alignItems="center"
                    sx={{
                      border: 1,
                      borderColor: 'text.primary',
                      height: '100px',
                      width: '100px',
                    }}
                  />
                ))}
              </Stack>
            ))}
          </Box>
        </Stack>
      </Typography>
    </ThemeProvider>
  );
}

export default App;
```

</TabItem>
</Tabs>

### 3. Add MakeMove()

We can now add in the ability for a player to make a move. We'll add an onClick handler to each tic-tac-toe square that will send a move containing the x- and y-coordinates (the row and column numbers of the box they clicked on) to the client. UrTurn will handle sending the move to your onPlayerMove function!


<Tabs>
<TabItem value="snippet" label="Snippet">

```js
onClick={async (event) => {
  event.preventDefault();
  const move = { x: rowNum, y: colNum };
  await client.makeMove(move);
}}
```

</TabItem>
<TabItem value="full" label="Full Code">

```js
import React, { useState, useEffect } from 'react';
import { ThemeProvider, Typography } from '@mui/material';

import client, { events } from '@urturn/client';
import theme from './theme';

function App() {
  const [boardGame, setBoardGame] = useState(client.getBoardGame() || {});
  useEffect(() => {
    const onStateChanged = (newBoardGame) => {
      setBoardGame(newBoardGame);
    };
    events.on('stateChanged', onStateChanged);
    return () => {
      events.off('stateChanged', onStateChanged);
    };
  }, []);

  console.log('boardGame:', boardGame);

  const {
    state: {
      board,
      winner,
    } = {
      board: [
        [null, null, null],
        [null, null, null],
        [null, null, null],
      ],
    },
    players = [],
    finished
  } = boardGame;

  return (
    <ThemeProvider theme={theme}>
      <Typography>
        <Stack margin={2} spacing={1} direction="row" justifyContent="center">
          <Box>
            {board.map((row, rowNum) => (
              <Stack key={rowNum} direction="row">
                {row.map((val, colNum) => (
                  <Stack
                    key={colNum}
                    direction="row"
                    justifyContent="center"
                    alignItems="center"
                    sx={{
                      border: 1,
                      borderColor: 'text.primary',
                      height: '100px',
                      width: '100px',
                    }}
                    onClick={async (event) => {
                      event.preventDefault();
                      const move = { x: rowNum, y: colNum };
                      await client.makeMove(move);
                    }}
                  />
                ))}
              </Stack>
            ))}
          </Box>
        </Stack>
      </Typography>
    </ThemeProvider>
  );
}

export default App;
```

</TabItem>
</Tabs>
