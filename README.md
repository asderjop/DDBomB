# 💣 DDR Bomb

A **Dance Dance Revolution-inspired rhythm game** built with Python and Pygame.  
Arrows fall from the top of the screen — hit them when they align with the purple targets, and **don't you dare press a bomb arrow!**

> Supports **English** and **Español** — language is selectable at startup.  
> Features **audio-synchronised beatmap generation** powered by [Librosa](https://librosa.org/).

---

## 🎮 Gameplay

| Action | Result |
|---|---|
| Arrow key matches a falling arrow at the right time | ✅ Points + combo |
| Perfect timing (dead center) | 💜 **PERFECT** — 100 pts |
| Good timing (close enough) | 🟢 **GOOD** — 50 pts |
| Miss (arrow passes the target) | ❌ Lose a life |
| Press a **bomb** arrow | 💥 Instant game over |
| Let a bomb arrow pass | ⭐ +25 bonus points |

- Every **8 resolved notes** you advance a level — arrows fall faster and bombs appear more often.
- Score multiplier grows with your combo (up to **×4**).
- You start with **3 lives**.

---

## 🕹️ Controls

| Key | Action |
|---|---|
| `←` `↓` `↑` `→` | Hit the corresponding lane |
| `ESC` | Quit |
| `L` | Change language (from Welcome or Game Over screen) |
| `ENTER` / `SPACE` | Start / Restart |
| `1` / `2` | Quick-select English / Español on the language screen |

---

## ✨ Features

- 🎵 **Audio-synced beatmap** — uses `librosa` onset detection to generate notes from any song file automatically
- 🔄 **Graceful fallback** — if no audio file is found, the game uses classic random time-based spawning so you can always play
- 💜 **Animated avatar** — reacts to your performance (happy, sad, neutral)
- 💣 **Bomb arrows** — styled with a pulsating red outline and a drawn bomb; increase in frequency with level
- 🌐 **Bilingual UI** — full English / Español support across all screens
- 📈 **Difficulty scaling** — fall speed and bomb chance increase every 8 notes
- ✨ **Explosion effect** — animated on bomb detonation
- 🎯 **Frame-perfect sync** — sub-frame time error is corrected via a Y-offset on spawn

---

## 🚀 Getting Started

### Prerequisites

- **Python 3.8+**
- **pip**

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/asderjop/DDBomB.git
cd DDBomB

# 2. Install dependencies
pip install pygame librosa
```

> `librosa` is optional but required for audio-synced beatmaps.  
> Without it the game runs in random-spawn mode.

### Running the game

```bash
python game.py
```

---

## 🎵 Adding Your Own Song

1. Drop any `.ogg`, `.mp3`, or `.wav` file into the project folder.
2. Open `game.py` and change the constant near the top:

```python
AUDIO_FILE = "your_song.ogg"   # ← set your filename here
```

3. Run the game — the beatmap is generated automatically from the audio's onsets.

> **Tip:** OGG Vorbis (`.ogg`) gives the best compatibility with Pygame's mixer.  
> MP3 works on most systems if `pygame` was built with MP3 support.

---

## 🗂️ Project Structure

```
DDBomB/
├── game.py          # All game logic, rendering, and audio analysis
├── bailando.mp3     # Default bundled song (replace or keep)
└── README.md        # This file
```

### Key sections inside `game.py`

| Section | Description |
|---|---|
| Constants | Screen size, lane layout, colors, timing windows |
| `generate_beatmap_from_audio()` | Standalone function — librosa onset detection → note dict list |
| `arrow_points()` / `draw_arrow()` | Polygon-based directional arrow rendering |
| `draw_bomb_arrow()` | Animated bomb arrow with pulsating outline |
| `draw_avatar()` | Expressive purple avatar (happy / sad / neutral) |
| `Note` | Data class for a falling note (lane, bomb flag, Y position) |
| `Game` | Main game class — state machine, update loop, drawing, events |

---

## ⚙️ Configuration

All tuneable constants live at the top of `game.py`:

| Constant | Default | Description |
|---|---|---|
| `SCREEN_WIDTH` / `SCREEN_HEIGHT` | `800` / `700` | Window size in pixels |
| `FPS` | `60` | Target frames per second |
| `SYNC_FALL_SPEED` | `400` | Arrow fall speed in px/s (audio-sync mode) |
| `AUDIO_FILE` | `"bailando.mp3"` | Song to analyse and play |
| `START_LIVES` | `3` | Starting lives |
| `PERFECT_WINDOW` | `25` px | Pixel tolerance for a PERFECT hit |
| `GOOD_WINDOW` | `75` px | Pixel tolerance for a GOOD hit |

---

## 🔧 How Audio Sync Works

```
Song file
   │
   ▼
librosa.load()  →  raw audio waveform
   │
   ▼
librosa.onset.onset_detect(backtrack=True)  →  onset frames
   │
   ▼
librosa.frames_to_time()  →  timestamps (seconds)
   │
   ▼
For each timestamp → pick lane (≠ previous) + roll bomb chance
   │
   ▼
Stored as deque of {"time", "lane", "bomb"} dicts
```

**During gameplay**, every frame:
```
travel_distance = TARGET_Y − spawn_Y        # pixels the note must travel
travel_time     = travel_distance / SYNC_FALL_SPEED   # seconds

if audio_time ≥ note["time"] − travel_time:
    spawn note
    note.y += time_error × SYNC_FALL_SPEED  # sub-frame correction
```

This ensures every arrow reaches the target zone at **exactly** the moment its onset plays in the song.

---

## 📦 Dependencies

| Package | Version | Purpose |
|---|---|---|
| `pygame` | ≥ 2.0 | Window, rendering, input, audio mixer |
| `librosa` | ≥ 0.10 | Audio loading and onset detection |

Install both at once:
```bash
pip install pygame librosa
```

---

## 📄 License

This project is open source. Feel free to fork, remix, and build on it.

---

## 🙏 Credits

- Built with [Pygame](https://www.pygame.org/) and [Librosa](https://librosa.org/)
- Inspired by [Dance Dance Revolution](https://en.wikipedia.org/wiki/Dance_Dance_Revolution)

