# One Minute Mic

You get a random topic and 60 seconds to talk about it out loud. The mic listens for one thing only: whether you are still talking. Go silent for more than a second and a half and you are buzzed out. Survive the full minute and your day streak goes up.

**[Play it in your browser](https://build-with-sarah.github.io/one-minute-mic/)**

It is a game, not a coach. No account, no server, no tips.

## Run it locally

The whole game is one file, but browsers only allow microphone access on `https://` or `localhost`, so serve it rather than double-clicking it.

1. Download `index.html`, or clone this repo.
2. In the folder, run `python3 -m http.server` (or any static server).
3. Open `http://localhost:8000` and allow the microphone when the browser asks.

## Privacy

Nothing is recorded and nothing is uploaded. There is no server. The game reads the microphone level frame by frame, asks "is this louder than the room?", and throws the number away. Your streak and stats live in your own browser's localStorage.

## Make it yours

Every topic lives in one place: the `DECKS` object at the top of the script in `index.html`. Edit the topics, add a deck, change the pause tolerances. The colour tokens sit at the top of the CSS. MIT licensed: do anything you want with it, keep the copyright line.

## How it was built

Designed with Claude Design, built with Claude Code, in one evening. The full design handoff the build was made from, seven boards and the behaviour spec, is in [`design/`](design/).

Made by [Sarah Murchú](https://buildwithsarah.dev).
