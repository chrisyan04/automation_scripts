# Python Version Update Automation :rocket:

This is the setup for the purpose of automating the process of updating your Python version on your OS.

**Purpose:** I am lazy and don't feel like always having to manually update this stuff.

## Mac :computer:

### Prequisites:

- have `pyenv` installed and properly configured on your OS
- use of `Terminal` (ie. command line)
- **NOTE:** the following steps involves scripting and a cron job

### Steps:

1. Create a new directory and a new shell script file in that directory.

```bash
mkdir scripts
cd scripts
touch auto_update_python.sh
```

2. Open that file in a text editor (in this case, just using TextEdit - mac default)

```bash
open -e auto_update_python.sh
```

3. Copy this script into the file.

```bash
#!/bin/bash

# Set path to your Python installation (.pyenv)
PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"

# Get the latest Python version available (via pyenv)
latest_version=$(pyenv install --list | grep -E "^\s*[0-9]+\.[0-50]+\.[0-50]+$" | tail -n 1)
# above includes regex to find version of form (x.y.z), where x,y,z ∈ ℕ, in pyenv

# Check if the latest version is already installed
if pyenv versions --bare | grep -q "$latest_version"; then
    echo "Python $latest_version is already installed."
else
    # Install the latest available Python version
    echo "Installing Python $latest_version..."
    pyenv install $latest_version
    pyenv global $latest_version
fi

# Log the update timestamp
echo "Python auto-update performed on $(date)" >> ~/python_auto_update_log.txt
```

4. Save that file.
5. Grant the file `auto_update_python.sh` permission to be executed as a program.

```bash
chmod +x auto_update_python.sh
```

6. Last step is to set up the cron job that runs the script periodically.
   1. Open the crontab editor (vi or nano).
   ```bash
   crontab -e
   ```
   2. Enter INSERT mode and add a line to schedule the script to run at a desired interval. In this case we are setting it to: 3 a.m. on a daily basis.
   ```bash
   0 3 * * * /path/auto_update_python.sh # in place of /path, put your path
   # if you don't recall your path to the script, run:
   pwd
   ```
   3. Save and exit the crontab editer.
      - **_( vi )_**: press `esc` -> type `:wq` -> press `enter`
      - **_( nano )_**: `Ctrl + O` (edit) -> `enter` (save) -> `Ctrl + X` (exit)
7. Now, you have properly setup the cron job that will automatically check for and install the latest Python version every day at 3 a.m. (mutable).

##
### Upon completion of steps above, follow diagram process:
![Screenshot 2024-01-19 at 3 28 13 PM](https://github.com/chrisyan04/automation_scripts/assets/86560918/42243b32-811b-4e2b-a945-ba7c9cedce5b)
