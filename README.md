# fshare2gdrive
NodeJS script for direct uploading from FShare.vn to Google Drive without storing files locally.
> For deprecated bash script (download.sh and login.sh), please see [here](https://github.com/duythongle/fshare2gdrive/blob/0cead7f9229fe6e54b2e9e81add0f6da4bdf453b/README.md).

## Dependencies

1. [RClone](https://rclone.org)

```bash
# Install rclone
curl -s https://rclone.org/install.sh | sudo bash
# Login
rclone config
```

Please see [RClone official documents support for Google Drive](https://rclone.org/drive/) for more details.

2. NodeJS and curl

``` bash
# Install on Ubuntu
sudo apt-get update && \
sudo apt-get install curl -y && \
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
```

## Usage

> This script is recommended to run on an unlimited bandwidth VPS or it will be getting costly over time

1. Install and login gdrive with rclone

``` bash
# Login
rclone config

```

Please see [RClone official documents support for Google Drive](https://rclone.org/drive/) for more details.

2. Install this script

``` bash
# Download this script
sudo curl -s -o /usr/local/bin/fshare2gdrive.js \
https://raw.githubusercontent.com/duythongle/fshare2gdrive/master/fshare2gdrive.js
# Make it executable
sudo chmod +x /usr/local/bin/fshare2gdrive.js


```

3. Download single FShare FILE to GDrive

``` bash
fshare2gdrive.js "<fshare_file_url>" "<rclone_remote_name>" "<remote_folder_path>"

```

`<fshare_file_url>`: your fshare file link.

`<rclone_remote_name>`: your rclone remote name that you have configured in step 1

`<remote_folder_path>`: your remote folder path you want to upload to.
> Don't forget to double quote your parameters

E.g:

``` bash
# the command below will download "https://www.fshare.vn/file/XXXXXXXXXXX"
# and pipe upload to "rclone rcat gdrive-remote:/RClone Upload/"
fshare2gdrive.js "https://www.fshare.vn/file/XXXXXXXXXXX" "gdrive-remote" "/RClone Upload/"
```

> If you run the command in the first time, it will ask for login FShare `username` and `password` then store login credentials to `$HOME/creds` in PLAIN TEXT. So use with caution!

3. Download whole FShare FOLDER to GDrive synchronously (one by one file) ***RECOMMENDED way***

``` bash
fshare2gdrive.js "<fshare_folder_url>" "<rclone_remote_name>" "<remote_folder_path>" | bash
```

`<fshare_folder_url>`: your fshare file link.

`<rclone_remote_name>`: your rclone remote name that you have configured in step 1

`<remote_folder_path>`: your remote folder path you want to upload to.
> Don't forget double quote your parameters

E.g:

``` bash
# The command below will download recursively all
# files of folder "https://www.fshare.vn/folder/XXXXXXXXXXX"
# and pipe upload to
# "rclone rcat gdrive-remote:/RClone Upload/fshare/folder/path/included/subfolder"
fshare2gdrive.js "https://www.fshare.vn/folder/XXXXXXXXXXX" "gdrive-remote" "/RClone Upload/" | bash

```

4. Download whole FShare FOLDER to GDrive in parallel ***(Use with caution!)***

You can run the jobs in parallel with [GNU Parallel](https://www.gnu.org/software/parallel/). E.g:

``` bash
# The command below will download recursively
# all files of folder "https://www.fshare.vn/folder/XXXXXXXXXXX"
# and pipe upload to
# "rclone rcat gdrive-remote:/RClone Upload/fshare/folder/path/included/subfolder"
# It will download in parallel with 2 simultaneous jobs
fshare2gdrive.js "https://www.fshare.vn/folder/XXXXXXXXXXX" "gdrive-remote" "/RClone Upload/" > \
temp && parallel -j 2 < temp

```

> Make sure all folders included subfolders are exist in remote folder path or rclone will create duplicated folders.
> If you keep getting ssh timeout issue, please make use of [Tmux](https://hackernoon.com/a-gentle-introduction-to-tmux-8d784c404340) or [ssh config file](https://stackoverflow.com/questions/25084288/keep-ssh-session-alive)
