# Lunar Lander: what makes an AI land well?

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/ryeggouti/lunar-lander-rl/blob/main/lunar_lander.ipynb)

**Part 1 of my reinforcement learning project.** I trained an AI to land a spacecraft on the Moon using only rewards, then changed the rules to see what its behaviour really depends on. Part 2 is [Stork Flight](https://github.com/ryeggouti/stork-flight), where I built my own environment and game.

The AI never sees the rules written down. It gets a score after every move (points for landing gently, a penalty for crashing, a small cost for every engine burn) and learns, by trial and error, what earns the most.

## Setup

- **Environment:** `LunarLander-v3` from [Gymnasium](https://gymnasium.farama.org/environments/box2d/lunar_lander/). A score of 200 or more counts as a solved landing.
- **Algorithm:** PPO from [Stable-Baselines3](https://stable-baselines3.readthedocs.io/), 8 environments in parallel.
- **Fair testing:** every AI is tested on the same 20 starting positions, and always scored with the normal rules, even if it trained with different ones.

## Results

### 1. It learns to land

| Pilot | Average score |
|---|---|
| Random button presses | −209 |
| AI after 100k steps of practice | −163 |
| AI after 300k steps | 3 |
| AI after 500k steps | 150 |
| AI after 1 million steps | 261 |

The random pilot was tested on 20 episodes and each checkpoint on 10. On the 20 fixed test starts used below, the 1-million-step AI landed every time, in about 7 seconds each.

### 2. Changing the rules (all trained for 500k steps)

| What I changed | Score | Landed | Crashed | Ran out of time | Seconds per landing |
|---|---|---|---|---|---|
| Nothing (normal rules) | 176 | 85% | 5% | 10% | 12.9 |
| **Free fuel** (engines cost nothing) | 86 | 55% | 0% | 45% | 18.4 |
| **No crash penalty** (a crash costs 0, not −100) | **271** | **100%** | 0% | 0% | **5.5** |

- **Free fuel made it worse.** With no cost for firing the engines, it fired the main engine about 50% more and hovered until time ran out in almost half of the tests.
- **Removing the crash penalty made it better**, which surprised me. It landed every time, more than twice as fast, and almost stopped using its side engines (27 side burns per landing instead of 268). It even beat the normal AI that trained twice as long.

### 3. Was that just luck? Three training runs each

One training run can be lucky, so I trained both versions again with two more random seeds.

| Setup | Scores (seeds 0, 1, 2) | Average | Landed |
|---|---|---|---|
| Normal rules | 176, 192, 196 | 188 | 85%, 60%, 80% |
| No crash penalty | 271, 256, 262 | 263 | 100% in all three |

The result held in every run.

**My best explanation** (a hypothesis, not something I proved): a −100 crash penalty teaches the AI that getting close to the ground is dangerous, so it learns to play it safe: hovering, correcting with the side engines, and sometimes running out of time. Without the penalty, it still wants the +100 for landing, so it commits and lands.

### 4. Wind

| Pilot | Tested in wind | Score | Landed | Crashed |
|---|---|---|---|---|
| Trained without wind (1M steps) | yes | 23 | 20% | 80% |
| Trained in wind (500k steps) | yes | 12 | 40% | 35% |

The AI that never felt wind crashed 80% of the time when it appeared. The wind-trained AI crashed less but still struggled, and it only had half the practice, so this comparison isn't completely fair.

## Limits

- 20 test landings per AI is a small sample.
- Only the crash-penalty experiment was repeated with three seeds. Free fuel and wind were trained once.
- The wind comparison mixes different training lengths (1M vs 500k steps).

## Run it yourself

Open the notebook in Colab with the badge at the top and run the cells in order. Training takes a while on Colab's free CPU, so keep the tab open. The notebook also records videos of the landings.

## Files

- `lunar_lander.ipynb`: training, experiments, seed tests and videos

---

I wrote this project with help from an AI coding assistant. The questions, the experiments and the conclusions are mine.
