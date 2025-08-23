# ImgBB Upload Script (`imgbb`)

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0) A simple Bash script to quickly upload images from the system clipboard (X11) or a specified file directly to [ImgBB](https://imgbb.com/) using their v1 API. It retrieves the direct image URL, prints it to STDOUT, and copies it to the X11 clipboard.

Ideal for quickly sharing screenshots or images in environments like IRC without needing to manually use the website interface.

**Author:** Ritchie Cunningham (Falco) <ritchie@ritchiecunningham.co.uk>

**Contributors:** dacav <dacav@fastmail.com>

**Version:** 2.0 (as of 25/04/2025)

## Features

* Upload images directly from the clipboard (PNG or JPEG).
* Upload images directly from a file path.
* Set an optional auto-deletion expiration time (e.g., `5m`, `2h`, `7d`, `0` for none).
* Specify a custom filename for the uploaded image on ImgBB.
* Optionally output and copy the URL in Markdown format.
* Optionally output and copy the URL in orgmode format.
* Takes screenshot of selected region.
* Takes screenshot of selected display.
* Copies the resulting URL to the X11 clipboard.
* Provides desktop notifications for progress and success/failure (optional).

## Dependencies

Make sure you have the following command-line tools installed:

* **curl:** Used for making the HTTP API request.
    ```bash
    sudo apt install curl # Debian.
    ```
* **jq:** Used for parsing the JSON API response.
    ```bash
    sudo apt install jq # Debian.
    ```
* **xclip:** Used for accessing the X11 clipboard. **This is for X11. Wayland users, see below..**
    ```bash
    sudo apt install xclip # Debian.
    ```
* **wl-clipboard** Used for accessing the Wayland clipboard. **This is for Wayland. X11 users, see above.**
* **notify-send (Optional):** Used for desktop notifications. The script works without it but won't show popups.
    ```bash
    sudo apt install notify-send # Debian (often in libnotify-bin).
    ```
* **scrot (Optional)**: Used for taking screenshots.
    ``` bash
    sudo apt install scrot # Debian.
    ```

## Installation

1.  **Clone the repository:**
    ```bash
    git clone git@github.com:hackers-libera/imgbb-uploader.git
    cd imgbb-uploader
    ```
2.  **Make the script executable:**
    ```bash
    chmod +x imgbb
    ```
3.  **Place in your PATH (Recommended):** Create a symbolic link to the script from a directory in your `$PATH` for easy execution (e.g., `~/.local/bin`, ensure this is in your `$PATH`).
    ```bash
    mkdir -p ~/.local/bin
    ln -s "$(pwd)/imgbb" ~/.local/bin/imgbb
    ```

## Configuration

This script requires a **free API key** from ImgBB:

1.  Go to [https://api.imgbb.com/](https://api.imgbb.com/) and register or log in to get your key.
2.  Set the API key using *one* of these methods (script checks in this order):
    *  Store the key in `.config/imgbb/uploader/api_key`.
      * **File** Move the `imgbb_uploader.conf` from the root directory of this project into `~/.config`.
      * Edit the file to include your API key, and optionally set up your own personal defaults.
          ```bash
          # Set permissions so only you can read it:
          chmod 600 ~/.config/imgbb_uploader.conf
          ```
      * **Environment Variable:** Set the `IMGBB_API_KEY` variable in your environment
          ```bash
          export IMGBB_API_KEY="YOUR KEY HERE"
          ```
    Now you can just run `imgbb` from your terminal.
    
## Usage
**Options:**

* `[filepath]` : Optional path to an image file to upload. If omitted, uses the clipboard content.
* `-e, --expire DURATION`: Set auto-deletion timeout. DURATION is a number followed by `s`(econds), `m`(inutes), `h`(ours), or `d`(ays) (e.g., `5m`, `2h`, `7d`, `300s`). Range: 60s-180d. Use `0`, `none`, or `never` for no expiration (overrides the default). Default expiration is 2 hours (7200s) if this option is not used.
* `-n, --name NAME`: Set a custom filename for the uploaded image on ImgBB.
* `--markdown`: Output and copy the URL in Markdown image format: `![](URL)`
* `--org`: Output and copy the URL in Orgmode image format: `[[url][alt text]]`
* `-s, --select`: Take screenshot of selected area for upload.
* `-M, --monitor NUM`: Take screenshot of monitor NUM (e.g., 0, 1) for upload.
* `-h, --help`: Show the help message and exit.

**Examples:**

```bash
# Upload image from clipboard, expire in 2 hours (default)
imgbb

# Upload specific image file, expire in 2 hours
imgbb screenshot.png

# Upload from clipboard, expire in 10 minutes
imgbb -e 10m

# Upload from clipboard, never expire
imgbb -e 0

# Upload file, give it a name on ImgBB, get Markdown output/copy
imgbb -n "Cool Diagram" --markdown diagram.jpg

# Take screenshot of selected region and upload
imgbb -s

# Take screenshot of display #2 and upload
imgbb -M 2

# Show help
imgbb -h

# Notes

-  Supports both X11 (using xclip) and Wayland (using wl-clipboard) sessions. It detects the session type using $XDG_SESSION_TYPE.
