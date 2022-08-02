---
title: Displaying the Game
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

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

Using our empty board, we can render a simple tic-tac-toe board:

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