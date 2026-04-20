# Development Environment

> This guide gets you from a fresh Raspberry Pi to a working Nymbra development environment. It assumes moderate comfort with Linux command lines, SSH, and Python. It does not assume familiarity with the SunFounder PiDog platform or with Nymbra specifically.

If you are only looking to run Nymbra, not develop it, see the Quick Start in the [project README](../README.md). This guide is for builders who want to modify code, run tests, or contribute back to the project.

---

## Scope of this guide

This document covers:

- Hardware assembly assumptions and what to verify before you start
- Operating system installation and base configuration
- Python environment setup with the tools Nymbra uses
- SunFounder PiDog SDK integration
- Repository clone, dependency install, and first-run verification
- Known issues and workarounds

It does not cover:

- Assembling the PiDog kit itself (see SunFounder's official assembly guide)
- Configuring network or SSH access beyond the minimum Nymbra needs
- Hardening the Pi for production deployment (Nymbra is a research platform, not a product)
- Platform-specific instructions for Raspberry Pi models other than the ones listed below

---

## Hardware requirements

### Supported platforms

Nymbra targets the following Raspberry Pi models:

- **Raspberry Pi 5 (8 GB)** — recommended. Best performance for local audio processing and LLM latency.
- **Raspberry Pi 5 (4 GB)** — supported with reduced performance margin. Expect higher latency when the uncertainty detection layer runs.
- **Raspberry Pi 4 Model B (8 GB)** — supported. Nymbra will run but will not meet the default latency targets for embodied interaction.

Older Pi models and the Pi Zero 2W are not supported. They lack the memory and CPU headroom to run Nymbra's conversational loop at usable latency.

### Additional hardware

- **SunFounder PiDog kit** — complete kit, assembled according to SunFounder's official instructions before starting this guide. Do not begin Nymbra setup on a half-assembled PiDog.
- **MicroSD card, 32 GB or larger** — Class 10 / UHS-I minimum. Lower-tier cards will work but will slow boot and package installation noticeably.
- **USB-C power supply** appropriate to the Pi model. The Pi 5 needs a 5V/5A supply for full performance; underpowered supplies cause silent performance degradation that looks like software problems.
- **Network access** — wired Ethernet preferred for initial setup. Wi-Fi works but adds troubleshooting surface.
- **Separate computer** for SSH access during setup. Headless setup is assumed; a monitor and keyboard are optional but not required.

### Pre-flight checks

Before you start, verify:

- The PiDog is fully assembled, calibrated per SunFounder's procedure, and powers on without servo errors
- The MicroSD card is empty or contains only data you are prepared to overwrite
- You have a way to reach the Pi over the network (wired is easier; Wi-Fi credentials ready if not)
- You have at least 90 minutes of uninterrupted time for a first-time setup

---

## Operating system installation

### Image selection

Install **Raspberry Pi OS (64-bit), Bookworm** or later. The 32-bit images will technically boot but lack the Python 3.11+ support Nymbra depends on without manual compilation.

Use the **Raspberry Pi Imager** (available for macOS, Windows, and Linux) to flash the SD card. Choose the "Raspberry Pi OS (64-bit)" option — not "Raspberry Pi OS Lite" unless you are comfortable installing desktop components manually later; Nymbra's calibration tools work better with the desktop environment present.

### Imager pre-configuration

In the Imager's advanced options (gear icon before flashing), set:

- **Hostname.** Something specific like `nymbra-dev` rather than the default `raspberrypi`. Hostnames make your life easier when you have multiple Pis on a network.
- **Username and password.** Do not use the default `pi` / `raspberry` credentials. Pick something memorable and write it down.
- **SSH.** Enable. Use password authentication for setup; switch to key authentication later.
- **Wi-Fi.** Configure here if you are not using Ethernet.
- **Locale settings.** Set your timezone and keyboard layout correctly. Log timestamps depend on the timezone being right.

Flash the card. Boot the Pi. Wait three to five minutes for first-boot configuration to complete before attempting SSH.

### First SSH connection

From your development machine:

```bash
ssh <username>@nymbra-dev.local
```

If `.local` resolution fails (common on some networks), find the Pi's IP address from your router and connect by IP.

### Base system updates

Once connected, update the system:

```bash
sudo apt update
sudo apt full-upgrade -y
sudo reboot
```

Reconnect after the reboot. The system is now current.

---

## Python environment

Nymbra targets **Python 3.11 or later**. Raspberry Pi OS Bookworm ships with Python 3.11, which is sufficient. Verify:

```bash
python3 --version
```

If the output is older than 3.11, stop and resolve this before continuing. Attempting to run Nymbra on older Python will produce confusing errors downstream.

### Virtual environment

Nymbra uses a project-local virtual environment. Do not install Nymbra's dependencies globally on the Pi.

Install `venv` support if it is not already present:

```bash
sudo apt install python3-venv python3-pip -y
```

The virtual environment itself is created in the repository clone step below.

### System packages

A small number of Nymbra's Python dependencies have native components that need system libraries:

```bash
sudo apt install -y \
    portaudio19-dev \
    libasound2-dev \
    libatlas-base-dev \
    libopenblas-dev \
    ffmpeg \
    git
```

These cover audio capture, NumPy acceleration, and media handling. Install them before creating the virtual environment to avoid mid-install errors.

---

## SunFounder PiDog SDK

Nymbra depends on SunFounder's PiDog Python SDK for hardware control. Install it before cloning Nymbra, because Nymbra's own `requirements.txt` assumes the SDK is available as a system package.

Follow SunFounder's current installation instructions at their official documentation site. At the time of writing, the canonical installation path is:

```bash
cd ~
git clone https://github.com/sunfounder/pidog.git
cd pidog
sudo python3 install.py
```

This installs the SDK globally on the system, which is the SunFounder-supported configuration. Nymbra's virtual environment will import the SDK from the system site-packages.

### SDK verification

Before continuing, verify the SDK installation:

```bash
python3 -c "from pidog import Pidog; print('SDK OK')"
```

If this fails, stop and resolve the SDK installation. Nymbra setup will not work on top of a broken SDK.

Calibrate the PiDog's servos using SunFounder's calibration procedure. An uncalibrated PiDog will behave unpredictably during Nymbra's embodied interaction testing.

---

## Nymbra repository setup

### Clone

```bash
cd ~
git clone https://github.com/<your-username>/nymbra.git
cd nymbra
```

Replace `<your-username>` with your GitHub username or the canonical Nymbra repository location.

### Virtual environment creation

Create a virtual environment with access to system packages (needed for the SunFounder SDK import):

```bash
python3 -m venv --system-site-packages .venv
source .venv/bin/activate
```

The `--system-site-packages` flag is important. Without it, the virtual environment cannot see the globally installed PiDog SDK.

The prompt should now show `(.venv)`. If it does not, the activation failed; re-run the `source` line.

### Dependency install

```bash
pip install --upgrade pip
pip install -r requirements.txt
pip install -r requirements-dev.txt
```

The split between `requirements.txt` (runtime) and `requirements-dev.txt` (tests, linting, documentation tooling) is intentional. A production deployment would install only the first; developers install both.

First install takes five to fifteen minutes depending on network speed and whether any wheels need to be compiled from source on the Pi.

### Environment variables

Nymbra uses environment variables for LLM API keys. Copy the template:

```bash
cp .env.example .env
```

Edit `.env` and add at least one LLM provider key. Nymbra will not start without at least one configured provider.

```bash
nano .env
```

The `.env` file is gitignored. Do not commit it.

---

## First-run verification

### Dry run

Run Nymbra in dry-run mode first. This exercises the imports and configuration loading without starting audio capture or making API calls:

```bash
python -m nymbra.main --dry-run
```

Expected output includes module load confirmations, configuration validation, and a clean exit. Any errors here indicate setup issues that need resolving before proceeding.

### Hardware check

Run the hardware verification script:

```bash
python scripts/hardware_check.py
```

This verifies:

- PiDog servos respond to test commands
- Microphone is detected and can capture a brief test sample
- Speaker is detected and can play a brief test tone
- Camera is detected and returns a single frame

All four should pass before attempting a full Nymbra session.

### First session

With the environment activated and the hardware check passed:

```bash
python -m nymbra.main
```

Nymbra starts in its default state: microphone off, awaiting an explicit verbal cue. See the [handler guide](handler-guide.md) for what to do next.

---

## Development workflow

Once the environment is set up, the day-to-day development loop is:

```bash
cd ~/nymbra
source .venv/bin/activate
git pull
pytest
```

The project uses:

- **pytest** for tests (configured in `pyproject.toml`)
- **ruff** for linting and formatting (configured in `pyproject.toml`)
- **mkdocs** for documentation builds (if you are editing docs locally)

Run the full test suite before committing:

```bash
pytest
ruff check .
ruff format --check .
```

The project CI runs the same checks; passing locally saves a round-trip.

---

## Known issues and workarounds

The following are documented gotchas, in approximate order of how often they trip up first-time setup.

### "ModuleNotFoundError: No module named 'pidog'" inside the virtual environment

The virtual environment was created without `--system-site-packages`. Recreate it:

```bash
deactivate
rm -rf .venv
python3 -m venv --system-site-packages .venv
source .venv/bin/activate
pip install -r requirements.txt -r requirements-dev.txt
```

### Audio capture returns silent frames

Most commonly caused by the microphone gain being set too low, or the wrong input device being selected. Run:

```bash
arecord -l
```

to list available capture devices, and update `NYMBRA_AUDIO_INPUT` in your `.env` file with the correct device index.

### PiDog movement is jittery or erratic

Calibration drift. Servos lose calibration over time, especially after transport or rough handling. Run SunFounder's calibration procedure. If jitter persists after calibration, check the power supply — underpowered USB-C adapters cause servo brownouts that look like software bugs.

### "Operation not permitted" when accessing the camera

The user account needs to be in the `video` group. Add it:

```bash
sudo usermod -aG video $USER
```

Log out and back in for group membership to take effect.

### pip install is extremely slow or hangs on specific packages

The Pi is compiling a package from source because no pre-built wheel exists for the architecture. This is normal for some scientific computing packages on ARM. Leave it running; most compile-from-source packages complete in under ten minutes.

If it hangs for more than thirty minutes on a single package, stop, check available RAM (`free -h`), and consider adding swap:

```bash
sudo dphys-swapfile swapoff
sudo nano /etc/dphys-swapfile   # change CONF_SWAPSIZE=100 to CONF_SWAPSIZE=2048
sudo dphys-swapfile setup
sudo dphys-swapfile swapon
```

Revert the swap change after the install completes — running on swap during normal operation hurts SD card life.

### Nymbra starts but session cue is never recognized

Check three things, in order:

1. The microphone is actually capturing audio (`arecord -d 5 test.wav && aplay test.wav`)
2. The session cue in your config file matches exactly what you are saying
3. Ambient noise is not overwhelming the signal. Try a quieter room before assuming the code is broken.

### LLM provider errors despite a valid API key

Most commonly caused by rate limiting or regional access issues, not by the key itself. Check the provider's status page and confirm your account has API access enabled (some providers gate API access separately from web UI access).

---

## Uninstall

To completely remove the Nymbra development environment:

```bash
cd ~
rm -rf nymbra
```

The SunFounder SDK and system packages installed during this guide are not removed. Remove them manually if needed.

---

## Appendix: full setup in one block

For experienced developers who want the condensed version, the full setup from a freshly imaged Pi:

```bash
# System updates
sudo apt update && sudo apt full-upgrade -y && sudo reboot

# After reboot:
sudo apt install -y python3-venv python3-pip portaudio19-dev \
    libasound2-dev libatlas-base-dev libopenblas-dev ffmpeg git

# SunFounder SDK
cd ~ && git clone https://github.com/sunfounder/pidog.git
cd pidog && sudo python3 install.py
python3 -c "from pidog import Pidog; print('SDK OK')"

# Nymbra
cd ~ && git clone https://github.com/<username>/nymbra.git
cd nymbra
python3 -m venv --system-site-packages .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt -r requirements-dev.txt
cp .env.example .env
# Edit .env to add LLM provider keys

# Verify
python -m nymbra.main --dry-run
python scripts/hardware_check.py
```

Use the condensed version only after you have done the full setup once and understood what each step does. Errors in condensed-setup mode are harder to diagnose because the diagnostic information is all compressed into one failed run.

---

## When this document is wrong

This guide is maintained alongside the code. When the setup procedure changes, the guide changes in the same pull request. If you encounter a setup step that does not match reality, that is a bug — please open an issue tagged `docs-bug` so it can be fixed.

The single most common cause of setup problems is a version mismatch between this guide and the SunFounder SDK's current installation procedure. The SDK is maintained by SunFounder, not by this project, and its installation steps occasionally change. If the SDK section above does not match SunFounder's current official documentation, trust theirs for the SDK steps and report the mismatch here.
