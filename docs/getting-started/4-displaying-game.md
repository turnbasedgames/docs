---
title: Displaying the Game
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Frontend

This section will go over how to implement the frontend for our tic-tac-toe so that it is visible to the user. We will be adding our components to ```frontend/src/App.jsx```. This file already contains some logic for you to access the [BoardGame](/docs/backend#boardgame) object and for any state changes to make to be propagated to your backend.

We will first extract the information we need from the board game state:

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


Using our empty board, we can render a simple tic-tac-toe board:

```mdx-code-block
<BrowserWindow>
```

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

```mdx-code-block
</BrowserWindow>
```

<Tabs>
<TabItem value="js" label="Snippet">

```js
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
<TabItem value="py" label="Live Demo">

```js live
def hello_world():
  print("Hello, world!")
```

</TabItem>
<TabItem value="java" label="Java">

```java
class HelloWorld {
  public static void main(String args[]) {
    System.out.println("Hello, World");
  }
}
```

</TabItem>
</Tabs>