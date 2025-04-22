# ImgBB Upload Script (`imgbb`)

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0) A simple Bash script to quickly upload images from the system clipboard (X11) or a specified file directly to [ImgBB](https://imgbb.com/) using their v1 API. It retrieves the direct image URL, prints it to STDOUT, and copies it to the X11 clipboard.

Ideal for quickly sharing screenshots or images in environments like IRC without needing to manually use the website interface.

**Author:** Ritchie Cunningham <ritchie@ritchiecunningham.co.uk>
**Version:** 1.0 (as of 22/04/2025)

## Features

* Upload images directly from the clipboard (PNG or JPEG).
* Upload images directly from a file path.
* Set an optional auto-deletion expiration time (e.g., `5m`, `2h`, `7d`, `0` for none).
* Specify a custom filename for the uploaded image on ImgBB.
* Optionally output and copy the URL in Markdown format.
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
* **xclip:** Used for accessing the X11 clipboard. **This script will not work on Wayland sessions.**
    ```bash
    sudo apt install xclip # Debian.
    ```
* **notify-send (Optional):** Used for desktop notifications. The script works without it but won't show popups.
    ```bash
    sudo apt install notify-send # Debian (often in libnotify-bin).
    ```

## Installation

1.  **Clone the repository:**
    ```bash
    git clone git@git.ritchiecunningham.co.uk:Rtch/imgbb-uploader.git
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
    Now you can just run `imgbb` from your terminal.

## Configuration

This script requires a **free API key** from ImgBB:

1.  Go to [https://api.imgbb.com/](https://api.imgbb.com/) and register or log in to get your key.
2.  Store the key in `.config/imgbb/uploader/api_key`.
    * **File** Create the directory and file, then paste *only* your API key inside:
        ```bash
        mkdir -p ~/.config/imgbb_uploader
        nano ~/.config/imgbb_uploader/api_key
        # Paste your key, save the file.
        # Set permissions so only you can read it:
        chmod 600 ~/.config/imgbb_uploader/api_key
        ```

## Usage
**Options:**

* `[filepath]` : Optional path to an image file to upload. If omitted, uses the clipboard content.
* `-e, --expire DURATION`: Set auto-deletion timeout. DURATION is a number followed by `s`(econds), `m`(inutes), `h`(ours), or `d`(ays) (e.g., `5m`, `2h`, `7d`, `300s`). Range: 60s-180d. Use `0`, `none`, or `never` for no expiration (overrides the default). Default expiration is 2 hours (7200s) if this option is not used.
* `-n, --name NAME`: Set a custom filename for the uploaded image on ImgBB.
* `--markdown`: Output and copy the URL in Markdown image format: `![](URL)`
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

# Show help
imgbb -h

# Notes

Currently relies on xclip and therefore only works reliably under X11 sessions. Wayland support would require adding logic to use wl-clipboard.
