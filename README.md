# Lk-Downloader

A fast, multi-threaded (IDM-style) download manager written in pure
Python, with a modern "ultra pro" dark desktop UI built on PyQt6.

## Features

- **YouTube (and other streaming site) downloads** — paste a YouTube
  link and it's downloaded via `yt-dlp` as a real, playable video
  file, saved under its actual title instead of the raw video id.
- **Multi-connection accelerated downloads** — splits a file into up
  to 32 parallel HTTP Range segments and reassembles them, just like
  Internet Download Manager.
- **Automatic single-stream fallback** for servers that don't support
  byte ranges, or when the file size can't be determined up front.
- **Pause / Resume / Stop** for every download, mid-transfer, with
  byte-accurate resuming.
- **Live speed, ETA and progress** — updated twice a second straight
  from the running download threads.
- **Add Download dialog** with clipboard auto-detect, custom save
  location, filename and connection-count control.
- **Sidebar filters** — All Downloads / Downloading / Completed /
  Paused / Failed / Queue / History.
- **Dashboard stat cards** — total downloads, active transfers,
  completed count and combined live speed.
- **Right-click menu** — start/resume, pause, stop, open file, open
  containing folder, copy URL, redownload, delete.
- **Persistent history** — your download list is saved to
  `database/history.json` and restored the next time you open the app.
- **Polished dark theme** — flat, rounded, accent-colored UI defined
  entirely in `gui/theme.py`.

## Requirements

- Python 3.10+
- PyQt6
- requests
- yt-dlp (for YouTube / video-site downloads)
- **ffmpeg** (optional, recommended) — if installed and on `PATH`,
  YouTube downloads are fetched in the best available quality and
  merged into a single `.mp4`. Without it, a single already-merged
  "best" stream is used instead (works fine, just capped at whatever
  quality YouTube serves progressively, usually up to 720p).

Install dependencies:

```bash
pip install -r requirements.txt
```

## Run

```bash
python main.py
```

## Project layout

```
Lk-Downloader/
├── main.py                     # entry point, applies theme, launches window
├── core/
│   ├── config.py                # app constants & paths
│   ├── task.py                  # DownloadTask data model (state machine)
│   ├── segment.py                # single Range-segment worker thread
│   ├── downloader.py            # download engine (multi/single stream, speed/ETA)
│   └── utils.py                  # formatting & filename helpers
├── gui/
│   ├── main_window.py            # main application window / all UI logic
│   ├── add_download_dialog.py    # "Add Download" dialog
│   ├── download_worker.py        # QThread bridge around the core engine
│   └── theme.py                   # ultra-pro dark QSS stylesheet
├── database/
│   └── history.py                # JSON-backed download history persistence
└── data/downloads/               # default save folder
```

## Notes

- Downloaded files are written directly to their final destination;
  multi-segment downloads pre-allocate the file and each thread writes
  into its own byte range, so no merge step is needed.
- If a download is still running when you close the app, you'll be
  asked whether to stop it; otherwise incomplete/queued items are
  saved and shown (as "Stopped") the next time you open Lk-Downloader.
