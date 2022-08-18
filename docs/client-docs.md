---
title: urturn/client
---

## High level Description

The `@urturn/client` provides a basic abstraction of communicating your frontend to the backend with a simple interface.

## Exported Methods

There are 3 main exported methods avaiable for frontend use:

- `getBoardGame`
- `makeMove`
- `getLocalPlayer`

## Example of use
Before getting into an example, it is critical to note the importance of the `makeMove` method. You can think of the method as a bridge between two states of your game, from a frontend perspective. This makes it easier to imagine the game in terms of discrete states when it comes to development work.

`old state -> makeMove -> new state`

In the `checkers` repo, there exists [use](https://github.com/turnbasedgames/tictactoe/blob/e36ab75d5e9c9a4165b17070971b79dae93585b2/frontend/src/App.jsx#L87) of the `makeMove` method.

The `getBoardGame` method is used to get the initial state of the game upon start up. It is up to you to perform updates to the game (via `makeMove`).
