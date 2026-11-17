# Loomwright 🧵
**A co-writer that remembers your universe and never contradicts itself.**

Loomwright is an AI storytelling assistant that helps authors build a world, branch into parallel universes, write in-character dialogue, and play through an interactive narrative, while keeping every fact consistent.

🎥 **Demo video:** [PASTE VIDEO LINK]
🌐 **Live app:** [PASTE DEPLOYED LINK, or delete this line]
💻 **Repository:** [PASTE GITHUB LINK]

---

## The problem
AI writing tools forget characters, break the rules of the world, and contradict themselves. Writers end up re-explaining their story every time they open a new chat.

## Our solution
Loomwright gives the AI a memory it can't lose:
- A structured **World Bible** (characters, locations, rules, quest) sent with every request
- A **running summary** of the story so far
- **Game state** (inventory, relationships, flags) tracked in Python code, not by the AI
- A **consistency checker** that flags contradictions against the World Bible

## Features
| Feature | What it does |
|---|---|
| World Bible generator | Turns a genre and premise into structured JSON: characters, rules, locations, quest |
| Interactive scenes | Writes a scene and offers 3 choices that continue the story |
| State tracker | Inventory, relationships, and flags update after every choice |
| "What if…" multiverse | Branches the story into a parallel universe that still obeys the world's core rules |
| Dialogue studio | Writes short exchanges in a character's own voice, using their goals and secrets as subtext |
| Story map | A graph of scenes, with one column per universe |
| Consistency checker | A second AI call that compares the latest scene to the World Bible |

## How it works
1. The user picks a genre and premise.
2. Claude returns a **World Bible** as JSON.
3. For each scene, the backend sends Claude: World Bible + story summary + current state + the player's choice.
4. Claude replies in JSON: `scene_text`, `choices`, `state_changes`, `summary`.
5. Our Python code (`apply_state`) applies the state changes. **The AI never owns the state.**
6. The frontend renders the scene, choices, state sidebar, and story map.

```
Player choice
     │
     ▼
 Frontend (HTML/JS) ──► FastAPI (main.py) ──► Claude API
     ▲                        │                    │
     │                        ▼                    ▼
     └──── scene + new state ◄── apply_state() ◄── JSON reply
```

## Tech stack
- **Language:** Python 3.10+
- **Backend:** FastAPI, Uvicorn, Pydantic
- **AI:** Claude API via the `anthropic` Python SDK (Sonnet for scenes and world-building, Haiku for quick checks)
- **Frontend:** HTML, CSS, and vanilla JavaScript (no build step), with an SVG story map
- **Config:** `python-dotenv` for the API key

## Built before the event vs. built live
| Pre-built (before event day) | Built live during the sprint |
|---|---|
| Figma wireframes | [e.g. Scene engine and choices] |
| Project setup, folder structure, UI shell | [e.g. State tracker (`apply_state`)] |
| Tuned World Bible prompt | [e.g. "What if" branching] |
| `call_json()` helper | [e.g. Consistency checker, dialogue studio] |
| | [e.g. Story map and polish] |

> Edit this table to match what you actually did. The Git history shows the split: see the `pre-event-baseline` tag and the commits after it.

## Run it locally

**Requirements:** Python 3.10 or newer and an Anthropic API key from [console.anthropic.com](https://console.anthropic.com).

```bash
# 1. Clone the repo
git clone [PASTE REPO URL]
cd loomwright

# 2. Create and activate a virtual environment
python -m venv venv
venv\Scripts\activate          # Windows
source venv/bin/activate       # Mac / Linux

# 3. Install dependencies
pip install -r requirements.txt
```

**4. Add your API key.** Copy `.env.example` to a new file named `.env` and fill it in:
```
ANTHROPIC_API_KEY=your_key_here
```

**5. Start the server:**
```bash
uvicorn main:app --reload
```

**6. Open** http://127.0.0.1:8000

> Never commit your `.env` file. It is already listed in `.gitignore`.

## API endpoints
| Method | Route | Purpose |
|---|---|---|
| GET | `/` | Serves the web app |
| POST | `/api/world` | Generates a World Bible from `premise` and `genre` |
| POST | `/api/scene` | Writes the next scene and returns the updated game state |
| POST | `/api/whatif` | Invents a parallel universe at the current moment |
| POST | `/api/check` | Finds contradictions between a scene and the World Bible |
| POST | `/api/dialogue` | Writes in-character dialogue for a chosen character |

FastAPI also generates interactive docs at http://127.0.0.1:8000/docs while the server is running.

## Project structure
```
loomwright/
├── main.py            # FastAPI backend: Claude calls, state logic, routes
├── static/
│   └── index.html     # Frontend UI
├── requirements.txt   # Python dependencies
├── .env.example       # Template for your API key
├── .gitignore         # Keeps secrets out of Git
└── README.md
```

## Screenshots
<!-- Add 2-3 screenshots to a /screenshots folder, then uncomment: -->
<!-- ![Start screen](screenshots/start.png) -->
<!-- ![Story and state sidebar](screenshots/story.png) -->
<!-- ![Parallel universe and map](screenshots/multiverse.png) -->

## Limitations and what's next
- Scenes appear all at once. Streaming word-by-word is planned.
- Stories are not saved between sessions. A database (SQLite) is the next step.
- Export to Twine or Markdown.
- Per-character voice sheets in a full Dialogue Studio tab.
- Multiplayer co-writing.

## Responsible use
Stories stay in the user's browser session and are sent to the Claude API only to generate text. The app is not intended to reproduce copyrighted works. Authors own what they create.

## Team
- [Name], [role]
- [Name], [role]

## License
MIT
