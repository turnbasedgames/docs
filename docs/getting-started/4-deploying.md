---
title: Deploying Your Game
---


## Build Your Game!

You are now ready to compile your frontend build!

```bash
cd frontend
npm run build
```

## Push Your Changes

If you haven't commited your changes yet, go ahead and do so and push to github! You **must** commit the build files as well.

```bash
git add .
git commit -m "first commit!"
git push
```

## Grab Your Commit Hash

You now need to find the commit containing the correct file versions you want to serve on UrTurn. You can access this through the GitHub UI or by running the following command:

```bash
git rev-parse HEAD
```

## Deploy to UrTurn!

Go to your [developer console](https://www.urturn.app/develop) and click **Create Game**. Give your game a name, link your github repo (no additional parameters! An example URL: https://github.com/turnbasedgames/tictactoe), and paste your commit hash. Click **Create**, and your game is now playable!