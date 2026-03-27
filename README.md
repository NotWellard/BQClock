# BQClock
A simple script to keep the clock on the be quiet! Dark Mount keyboard synced and accompanying systemd files, rather than needing to run an entire Chromium instance with the [web application](https://iocenter.bequiet.com) in it.

## AI Disclosure 
This script was created by Anthropic's Claude Sonnet 4.6, while I think this is a perfectly acceptable usage of AI as it's not replacing a human's job I recognise not everyone thinks this way. It's your choice whether you engage with or use this.

## Permissions
If you plan to run the script as root you can skip this part, but I'd recommend doing it anyway to allow you to use [IOCenter](https://iocenter.bequiet.com) to control other aspects of your keyboard.

be quiet! provides a script for upodating udev rules to provide your user permissions to allow control of the keyboard without root:  
`curl -o- https://iocenter.bequiet.com/update-udev.sh | sudo bash`  

After running this, try accessing [IOCenter](https://iocenter.bequiet.com) in a chromium-based browser or running the script manually.

If you try it and it doesn't work, your distro likely uses `input` instead of `plugdev`, run the following:  
`sudo sh -c "usermod -aG input $USER && sudo sed -i 's/plugdev/input/g' /etc/udev/rules.d/10-bequiet.rules && udevadm control --reload-rules && udevadm trigger"`  
This will add you to the input group, replace `plugdev` with `input` in the udev file created by the be quiet! script and reload the udev rules.

## Dependancies
This project depends on `python3` and `python3-hidapi`, both of these must be installed on your distro before deployment or the script will fail!

Most distros come with `python` pre-installed but not `python3-hidapi`, below are commands for installing both packages on major distros for your convenience:

Debian: `sudo apt install python3-hidapi`  
Fedora: `sudo dnf install python3-hidapi`  
Arch: `sudo pacman -S python-hidapi`

## Deployment
### Cron
1) Download the bqclock file.
2) Put it in a logical place (`/usr/local/bin/` or `/root/.local/bin/`)
3) Make the file executable: `sudo chmod 755 /path/to/bqclock`
4) run `sudo crontab -e` and add: `* * * * * /path/to/bqclock` (This will run at the turn of every minute, you can use a [crontab calculator](https://crontab.guru) if you want to make it run more infrequently)

### Systemd
1) Put the `bqclock` file in `/usr/local/bin`,
2) Make the file executable: `sudo chmod 755 /usr/local/bin/bqclock`
3) put both `bqclock.service` and `bqclock.timer` in `/etc/systemd/system/`
4) Run `sudo systemd enable --now bqclock.timer`