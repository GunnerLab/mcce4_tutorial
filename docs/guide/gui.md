---
layout: default
title: MCCE4 GUI
parent: Guide
nav_order: 9
---

# MCCE4 Web GUI
{: .no_toc }

A modern browser-based interface for running, monitoring, and analyzing MCCE4 simulations — no X11 forwarding required.
{: .fs-5 .fw-300 }

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview

`mcce4-gui` is a lightweight web server that runs alongside MCCE4 on your compute node. Instead of forwarding a desktop application over SSH, it serves a modern interface to your local browser. You get real-time log streaming, interactive 3D protein visualization, clickable parameter controls, and post-simulation analysis — all through an SSH tunnel.

**Key features:**

- **Pipeline runner** — Configure and execute all four MCCE4 steps with a visual progress tracker
- **3D structure preview** — Interactive protein visualization via [3Dmol.js](https://3dmol.csb.pitt.edu/) (no PyMOL required on the server)
- **Server file browser** — Navigate the remote filesystem to select PDB files without typing paths
- **Live log streaming** — Real-time output via WebSocket connection
- **Stop & resume** — Interrupt long computations and pick up where you left off
- **SLURM submission** — Generate and submit `sbatch` scripts for HPC clusters
- **Post-simulation analysis** — Interactive titration curves and net charge plots via [Plotly](https://plotly.com/javascript/)
- **Light & dark mode** — Automatically follows your OS preference

---

## Prerequisites

`mcce4-gui` requires a few additional Python packages beyond the standard MCCE4 dependencies. If you are using the `mc4` conda environment, these are already included in the `mc4.yml` file:

```yaml
dependencies:
  - fastapi
  - uvicorn
  - python-multipart
  - websockets
```

If you need to install them manually:

```bash
conda activate mc4
pip install fastapi uvicorn python-multipart websockets
```

---

## Quick Start

### 1. Start the GUI server

Navigate to your MCCE4 working directory and launch the GUI:

```bash
cd /path/to/your/protein_workdir
mcce4-gui --mcce-home ~/MCCE4
```

You should see output like:

```
  ╭───────────────────────────────────────────╮
  │            MCCE4 Web GUI v1.0.0            │
  ├───────────────────────────────────────────┤
  │  URL:      http://127.0.0.1:8080          │
  │  Workdir:  /path/to/your/protein_workdir  │
  │  MCCE:     /home/user/MCCE4               │
  ╰───────────────────────────────────────────╯

  SSH session detected — browser can't open on
  the remote server. On your LOCAL machine, run:

    ssh -L 8080:localhost:8080 user@server

  Then open http://localhost:8080 in your local browser.
```

### 2. Set up the SSH tunnel

On your **local machine**, open a new terminal and create the tunnel:

```bash
ssh -L 8080:localhost:8080 user@your-server
```

This forwards port `8080` on your local machine to port `8080` on the remote server. The tunnel stays open as long as the SSH connection is alive.

### 3. Open in your browser

Navigate to [http://localhost:8080](http://localhost:8080) in any modern browser (Chrome, Firefox, Safari, or Edge).

{: .note }
If port 8080 is already in use, specify a different port: `mcce4-gui --port 9090` and adjust your SSH tunnel accordingly.

---

## CLI Options

```
mcce4-gui [OPTIONS]

Options:
  --port, -p INT       Port to serve on (default: 8080)
  --host TEXT          Host to bind to (default: 127.0.0.1)
  --no-browser         Don't auto-open browser on startup
  --mcce-home TEXT     Path to MCCE4 installation directory
  --workdir, -w TEXT   Working directory for MCCE4 runs
```

**Examples:**

```bash
# Start with defaults
mcce4-gui --mcce-home ~/MCCE4

# Custom port
mcce4-gui --port 9090 --mcce-home ~/MCCE4

# Specify working directory explicitly
mcce4-gui --mcce-home ~/MCCE4 --workdir ~/proteins/4pti
```

---

## Using the Pipeline Tab

The **Pipeline** tab is the main workspace for running MCCE4 simulations.

### Selecting a PDB file

There are two ways to load a protein structure:

1. **Browse** — Click the **Browse** button next to the PDB file input. A file browser modal opens, allowing you to navigate the server filesystem. Double-click directories to enter them, single-click a `.pdb` file to select it, and double-click or press **Select** to confirm.

2. **Upload** — Click **Upload** to send a PDB file from your local machine to the server's working directory.

Once a PDB file is loaded, the **3D structure preview** panel renders the protein using 3Dmol.js with a spectrum-colored cartoon representation. You can rotate, zoom, and pan the structure interactively.

### Configuring parameters

The left panel organizes all MCCE4 parameters into collapsible sections:

| Section | Key parameters |
|:--------|:---------------|
| **MCCE paths** | `MCCE_HOME` directory, optional `EXTRA.tpl` file |
| **Steps to run** | Enable/disable individual steps (1–4) |
| **Global parameters** | Inner dielectric (default: 4.0), outer dielectric (default: 80.0) |
| **Step 1 — Preparation** | Dry run toggle (remove waters) |
| **Step 2 — Rotamers** | Conformer level: 1 (isosteric), 2 (heavy atom), 3 (comprehensive) |
| **Step 3 — Energies** | PBE solver (ngpb/delphi/zap), number of processes, salt concentration |
| **Step 4 — Monte Carlo** | Initial pH/Eh, interval, number of titration steps, type (pH or Eh) |

### Running the pipeline

Click **Run pipeline** to start execution. The visual stepper at the top shows real-time progress:

- **Gray circle** — Pending
- **Green pulse** — Running (with elapsed time)
- **Green checkmark** — Completed (with total time)
- **Red ✗** — Failed

The **Live output** panel on the right streams `stdout` from each step in real time. Log lines are color-coded:

- Green — Step headers and completion messages
- Red — Errors
- Gray — Timestamps and routine output

### Stopping and resuming

- **Stop** — Terminates the current step. The pipeline enters a "paused" state.
- **Resume** — Restarts from the interrupted step (not from the beginning).

### Saving and loading configurations

Click **Save config** to persist all current parameter settings to `mcce4_gui_config.json` in the working directory. Click **Load config** to restore them. This is useful for reproducing runs or switching between parameter sets.

---

## Using the Configuration Tab

The **Configuration** tab provides additional settings for execution mode and SLURM job submission.

### Local vs. SLURM execution

Select **Local execution** to run MCCE4 steps directly as subprocesses (default). Select **SLURM submission** to generate and submit an `sbatch` script instead.

### SLURM submission

When SLURM mode is selected, configure your job parameters:

| Parameter | Description | Default |
|:----------|:------------|:--------|
| Job name | SLURM job identifier | `mcce_job` |
| CPUs | `--cpus-per-task` | 4 |
| Memory | `--mem` in GB | 24 |
| Partition | `--partition` (leave empty for default) | — |
| Time limit | `--time` | `24:00:00` |
| Extra directives | Additional `#SBATCH` lines (one per line) | — |

**Preview script** — Click to view the exact `sbatch` script that will be generated, including all step commands and SLURM directives. Review it before submitting.

**Submit SLURM job** — Submits the script via `sbatch`. The GUI displays the assigned job ID and polls `sacct`/`squeue` to show job status (PENDING → RUNNING → COMPLETED).

---

## Using the Analysis Tab

The **Analysis** tab provides interactive visualization of MCCE4 output after Step 4 completes.

### Loading results

Click **Load results** to parse `pK.out` and `sum_crg.out` from the working directory. You can also click **Check output files** to verify which output files exist before loading.

### pKa results table

The left panel displays all titratable residues with their computed values:

| Column | Description |
|:-------|:------------|
| Residue | Residue identifier (e.g., `ASP-A0003_`) |
| pKa/Em | Computed pKₐ or Eₘ value (shows `>14.0` or `<0.0` for out-of-range) |
| n(slope) | Slope of the titration curve at the midpoint |
| 1000*chi2 | Goodness of fit (lower = better) |

### Titration curves

Click any row in the pKa table to plot that residue's charge vs. pH curve from `sum_crg.out`. Selected rows are highlighted in green. You can select multiple residues to overlay their curves on the same plot.

- **Select all** — Plot all titratable residues at once
- **Clear** — Remove all curves from the plot

The titration chart is interactive (powered by Plotly): hover for data values, zoom by dragging, and pan by holding shift.

### Net charge vs. pH

The bottom chart automatically displays the total net charge of the protein across the titration range. This plot renders immediately when results are loaded — no selection needed.

---

## Architecture

The GUI consists of a Python backend (FastAPI) and a single-file HTML/JS frontend:

```
MCCE4-Alpha/bin/
├── mcce4-gui              # Launcher script (bash)
├── mcce4_gui/             # Python package
│   ├── __init__.py
│   ├── __main__.py        # CLI entry point
│   ├── app.py             # FastAPI server (REST + WebSocket)
│   ├── config.py          # Configuration models
│   ├── pipeline.py        # Async pipeline manager
│   ├── slurm.py           # SLURM sbatch generation & polling
│   ├── analysis.py        # Output file parsers (pK.out, sum_crg.out)
│   └── static/
│       └── index.html     # Frontend SPA (vanilla JS + 3Dmol.js + Plotly)
```

The server is single-user by design — it manages one pipeline at a time in the current working directory, which matches the typical MCCE4 workflow of one protein per directory.

---

## REST API

All GUI functionality is accessible programmatically through a REST API. This can be useful for scripting or integrating with other tools.

| Endpoint | Method | Description |
|:---------|:-------|:------------|
| `/api/config` | GET | Return current configuration |
| `/api/config` | POST | Update configuration |
| `/api/config/save` | POST | Persist config to `mcce4_gui_config.json` |
| `/api/pipeline/run` | POST | Start the pipeline |
| `/api/pipeline/stop` | POST | Stop running pipeline |
| `/api/pipeline/resume` | POST | Resume from last incomplete step |
| `/api/pipeline/status` | GET | Get step statuses and timing |
| `/api/files/browse` | GET | Browse server filesystem |
| `/api/upload/pdb` | POST | Upload a PDB file |
| `/api/pdb/content` | GET | Get PDB file content (for 3D viewer) |
| `/api/slurm/submit` | POST | Submit SLURM job |
| `/api/slurm/status` | GET | Poll SLURM job status |
| `/api/slurm/preview` | GET | Preview sbatch script |
| `/api/slurm/cancel` | POST | Cancel SLURM job |
| `/api/analysis/pka` | GET | Parsed pK.out data |
| `/api/analysis/sum_crg` | GET | Parsed sum_crg.out data |
| `/api/analysis/available` | GET | Check which output files exist |
| `/ws` | WebSocket | Real-time log streaming |

**Example — check pipeline status from the command line:**

```bash
curl http://localhost:8080/api/pipeline/status | python -m json.tool
```

---

## Troubleshooting

### Port already in use

If you see `Address already in use`, another process is using port 8080. Either stop it or use a different port:

```bash
mcce4-gui --port 9090 --mcce-home ~/MCCE4
# Adjust tunnel: ssh -L 9090:localhost:9090 user@server
```

### Connection refused in browser

Make sure your SSH tunnel is active. The tunnel must forward the same port the GUI is running on:

```bash
# Server side:
mcce4-gui --port 8080 --mcce-home ~/MCCE4

# Local side (separate terminal):
ssh -L 8080:localhost:8080 user@server
```

### 3D viewer shows blank

The 3Dmol.js library loads from a CDN. If your browser has no internet access, the viewer won't render. The rest of the GUI works normally without it.

### Analysis plots are empty

Ensure Step 4 has completed and that `pK.out` and `sum_crg.out` exist in the working directory. Click **Check output files** to verify.
