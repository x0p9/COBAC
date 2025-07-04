## Bulk Account Checker (CLI)

# Critical Ops Bulk Account Checker

A command-line tool that scans huge name lists and tells you which usernames are **taken**, **available**, or temporarily **blocked (403)** on Critical Ops.

---

## Key Features

* Reads any plain-text list of candidate names (one per line).
* Respects C-OPS length rules (3-16 characters).
* Configurable rate-limit (default **60 requests/min**).
* Live progress bar with ETA, counts for *found*, *unavailable* and *403* hits.
* Pause / resume with <kbd>Ctrl-C</kbd> & <kbd>Enter</kbd>; press <kbd>Ctrl-C</kbd> twice to stop cleanly.
* Writes three output files you can inspect or reuse later:
  * `sorted/found-words.txt`     – usernames that appear **available** (no player profile found).
  * `sorted/unavailable.txt`     – usernames that are **already taken**.
  * `sorted/403-error.txt`       – names that triggered **403 Forbidden** (API anti-bot).  Retry these later.

---

## Quick Start

```bash
# 1. clone & enter repo
# 2. create env (or reuse final-gui-venv)
python -m venv venv && source venv/bin/activate

# 3. deps
pip install -r requirements.txt   # requests, colorama

# 4. run   (uses usernames/english-dictionary.txt by default)
python bulk_check.py
```

Need a fast test?
```bash
python test_bulk_check.py   # only 9 sample names, finishes in seconds
```

---

## How It Works

1. Load the *input list* (change `INPUT_FILE` at the top of `bulk_check.py`).
2. For every candidate name, call `copspy.get_profile.get_player_by_ign()`.
3. Sleep `DELAY = 60 / RATE_LIMIT` seconds (+ small jitter) to stay under the limit.
4. Categorise the response:
   * **OK + JSON**   → name exists → append to *found* file.
   * **500 / not-found**   → name free → append to *unavailable* file.
   * **403 Forbidden**   → API blocked request → append to *403* file.
5. Update the console line with fresh stats & ETA.
6. On pause / resume / exit, already processed names remain safely on disk.

---

## Tweaking

Open `bulk_check.py` and customize these settings:

### Rate Limiting & Performance
```python
RATE_LIMIT = 30          # requests per minute (lower = safer, higher = faster)
DELAY = 60 / RATE_LIMIT  # auto-calculated sleep between requests
```

### Input/Output Files
```python
INPUT_FILE = Path("your_list.txt")                    # change input source
FOUND_FILE = Path("sorted/found-words.txt")       # available usernames
NOT_FOUND_FILE = Path("sorted/unavalible.txt")       # taken usernames  
ERROR403_FILE = Path("sorted/403-error.txt")      # 403 errors
```

### Username Filtering
```python
MIN_LEN, MAX_LEN = 3, 16  # Critical Ops username length limits
# Filter logic: usernames = [w for w in all_words if MIN_LEN <= len(w) <= MAX_LEN]
```

### Advanced Options
- **Random jitter**: The script adds `random.uniform(0, 0.3)` seconds to each delay to appear more human-like
- **Pause/Resume**: Press Ctrl+C once to pause, Enter to resume, Ctrl+C twice to exit
- **Progress tracking**: All counters and ETA calculations update in real-time

**Pro tip**: Lowering the rate limit or using a VPN/proxy rotation usually eliminates most 403 errors.

---

## Looking to Buy an IGN List?
## Want to Purchase This Bot?
If you're interested in purchasing this Critical Ops username checker, reach out on Discord:

* **x0p9**  
* **corixe**

I'll get back to you with current pricing and delivery options for **the bot**. We also sell curated IGN lists.

---

## Credits
* **copspy** – Critical Ops public API wrapper by [Kitsune](https://github.com/Kitsune-San).
* This checker is just a thin layer on top of her excellent work – thank you!

> This project is not affiliated with Critical Force or Critical Ops.  It is provided *as-is* by the community, for the community.
