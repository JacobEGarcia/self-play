# SELF-PLAY TERMINAL

Two tabular Q-learning agents teach themselves Pong through adversarial self-play,
live in the browser. No neural network, no weights, no server, no build step.

Open `index.html` in any modern browser, or serve the folder with any static
file server. Training starts automatically at MAX speed and autosaves to
localStorage, so the agents keep their skill between visits on the same machine.

## What you see

- OBSERVATION DECK: the current champion (left, amber) defends against a scripted
  drone in real time. Sets are first to 7.
- LEARNING CURVE: amber line is the champion's win rate against the drone,
  evaluated every 100 episodes. The faint white line is average rally length.
- CHALLENGE THE CHAMPION: play the frozen champion yourself (W/S or arrow keys,
  first to 5, ESC to leave). Training keeps running in the background.

## How it learns

Each paddle is a tabular Q-learning agent. The court is discretized into 900
states (ball position bucket, heading, paddle offset) with 3 actions
(up / hold / down). Reward: +1 for a point, -1 for a concession, +0.2 per
return, plus a small shaping reward for closing on the ball. Epsilon-greedy
exploration decays from 0.30 to 0.03. The entire "brain" is a table of 2,700
floats updated with the standard Q-learning rule.

Two of every three training games are self-play between the agents; the third
is a sparring round against the scripted drone. Every 100 episodes the training
policy faces the drone in a seeded 41-game evaluation, and when it beats the
reigning champion it takes the crown. The champion is therefore a checkpoint,
like the historical snapshots in large self-play systems: the amber win line
cannot go down, while the raw candidate policy underneath oscillates as it
keeps exploring.

This is the self-play loop from Stanford's reinforcement learning lecture
(CS234): skill bootstrapped by playing against yourself with zero human
examples - the same result the AlphaGo work reported at scale, where the
self-play version outperformed the one trained on human games.

## Notes

- Original code, no dependencies, no network calls after the font load.
- RESET TRAINING wipes the local archive and starts from zero.
- Desktop-first layout; works in any viewport but the keyboard challenge mode
  needs a keyboard.
