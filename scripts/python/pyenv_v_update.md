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

# Set path to your Python installation (pyenv)
PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"

# Your log file path
log_file="$HOME/.../py-v-update-logs.txt"

# Function to log messages with timestamp
log_message() {
    echo "$(date +"%Y-%m-%d %H:%M:%S"): $1" >> "$log_file"
}

# Latest Python version via pyenv
latest_version=$(pyenv install --list | grep -E "^\s*[0-9]+\.[0-50]+\.[0-50]+$" | tail -n 1 | tr -d '[:space:]') # remove spaces

# Current installed Python version
installed_version=$(python3 -V 2>&1 | awk '{print $2}' | tr -d '[:space:]') # remove spaces

log_message "Currently installed Python version: $installed_version"

# Check if the latest version is already installed
if [ "$installed_version" == "$latest_version" ]; then
    log_message "Latest Python version $latest_version is already installed. No action needed."
else
    log_message "Installing latest Python version: $latest_version"

    # Install the latest version ("yes" automatically accepts the update)
    yes | pyenv install --skip-existing $latest_version 2>&1 | tee -a "$log_file"
    
    # Set installed version as the global version
    pyenv global $latest_version

    log_message "Python $latest_version installation completed."
fi
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
   2. Enter INSERT mode and add a line to schedule the script to run at a desired interval. In this case we are setting it to: midnight on a daily basis.
   ```bash
   0 0 * * * /path/auto_update_python.sh # in place of /path, put your path
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
