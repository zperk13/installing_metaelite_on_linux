# Installing MetaElite on Linux
## Preamble
[MetaElite](https://raxxla.org/) can run via Wine/Proton on Linux. However, there are some tweaks that you can make to make the experience better. Also, if you've never installed stuff for proton before without Steam doing everything for you, it may not be obvious how to do things. I will attempt to make this an easy step-by-step guide to get MetaElite working on your system. You will need to run commands to get everything set up, but by the end of the setup, you'll be able to launch MetaElite like any other application on your computer. If you are having trouble with anything in this guide, feel free to @ me in the #linux channel on the [MetaElite Discord](https://discord.com/invite/BnMdzncKW6). My username there is Zoe.

## Installation
1. [Install winetricks](https://github.com/Winetricks/winetricks?tab=readme-ov-file#installing)
2. [Install protontricks](https://github.com/Matoking/protontricks?tab=readme-ov-file#installation). There are several options for installing it. You can use whatever works best for you as long as you get the latest version, but I recommend using [pipx](https://github.com/pypa/pipx). If you do want use pipx, see the [pipx section](#pipx).
3. Open Steam, go to your Library, select Elite Dangerous (don't launch it), and click the gear icon
<img width="959" height="341" alt="Cropped screenshot of Steam with a red arrow pointing to wear the gear is. It is to the left of the heart and info symbol, on the right of the window." src="https://github.com/user-attachments/assets/2ea494a4-626e-418a-839f-4242a629e8f2" />

4. Click "Compatibility"
5. Make sure the "Force the use of a specific Steam Play compatibility tool" checkbox is checked
6. In the drop down menu, select Proton 10.0-3
7. Download MetaElite from https://raxxla.org/. The downloaded file is a `.msi` file and you will not need to keep it, so you can just throw it in your downloads folder or something.
8. In the terminal, `cd` into the folder with the `.msi` file. If you put it in your downloads folder, the command will probably be `cd ~/Downloads`
9. Run `protontricks-launch --appid 359320 ./MetaElite-1.02.msi` (Update 1.02 may not be the latest version if you're reading this in the future) (359320 is the ID for Elite Dangerous)
10. You should now be seeing the installer
<img width="482" height="344" alt="Picture of the installer. It says Welcome to the MetaElite Setup Wizard. The Setup Wizard will install MetaElite on your computer. MetaElite integrates community-driven rote playing with the Elite: Dangerous game. Click Next to continue or Cancel to exit the Setup Wizard." src="https://github.com/user-attachments/assets/cc5fd172-caca-46dc-83e0-4fb992ed114d" />

11. Click "Next"
12. You should now see the End-User License Agreement. I'm sure you know what to do here
<img width="475" height="347" alt="Picture of the EULA section of the installer" src="https://github.com/user-attachments/assets/7ddb06d4-0cc9-452b-bd60-5c32bb570c31" />

13. You should now bee seeing the option to select the destination folder. Leave it as the default `C:\Program Files\raxxla.org\MetaElite\` and click "Next"
<img width="487" height="348" alt="Picture of the installer. It says Destination Folder. Click Next to install to the default folder or click Change to choose another." src="https://github.com/user-attachments/assets/96abe602-ca12-4dec-9a52-8f1d214cc439" />

13. You should now be seeing the option to create a desktop and start menu shortcut. Uncheck both of these boxes. Because this is running via wine/proton, it will not do what you expect it to do, and will just create unnecessary files. Then click "Next"
<img width="490" height="358" alt="Picture of the installer. At the top it says Installation Options. Select which shortcuts you want to create. Both the Create Desktop Shortcut and Create Start Menu shortcut checkboxes are unchecked." src="https://github.com/user-attachments/assets/cb5db5ba-dd0b-4a0b-ab54-9867212808e7" />

14. Now you just have to click "Install"
<img width="487" height="355" alt="Picture of the installer. It says Ready to install MetaElite. Click Install to begin the installation. Click Back to review or change any of your installation settings. Click Cancel to exit the wizard." src="https://github.com/user-attachments/assets/dda3065e-b473-4c84-8d48-d278cc372aa8" />

Congrats! MetaElite is now installed. However, it is not convenient to launch and there are some tweaks that can be made, so continue reading.

## Launching it like a normal application
To make it so you can launch MetaElite, there are two steps. Making it a simple command, and making a desktop file. The first part isn't technically neccesary, you can include the script in the desktop file, but that doesn't look great, and will be tricky to work with if you use one of the longer options for launch scripts.

If you have a place in particular where you'd like to keep the script that's in `$PATH` or you're fine with editing future references to the command to have a full path, go for it, otherwise, we'll be putting the script in `/usr/local/bin`. 

In your file explorer or terminal, navigate to `/usr/local/bin`. Make a file here called `MetaElite`. You will need root perms to add a file here. If you're in your terminal, you can do `sudoedit /usr/local/bin/MetaElite`. Then set the contents of the file to the following and save it.
```bash
#!/usr/bin/env bash
elite_dangerous_id=359320
pfx="$(protontricks --command 'echo $WINEPREFIX' "$elite_dangerous_id" 2>/dev/null)"
cd "$pfx/drive_c/Program Files/raxxla.org/MetaElite" || exit
protontricks-launch --appid "$elite_dangerous_id" ./MetaElite*.exe
```

Now that you have the script file, you need to make it executable, otherwise Linux will just think it's a normal text file. To do that, run this command `sudo chmod +x /usr/local/bin/MetaElite` (+x means make it e***X***ecutable).

Now we just need to make the desktop file. Go to `~/.local/share/applications` (the folder may need to be made) and make a file there called MetaElite.desktop and set the contents of it to the following:
```
[Desktop Entry]
Type=Application
Name=MetaElite
Exec=MetaElite
Terminal=false
```

Save the file and now you should be able to launch MetaElite like any other application!

## Launch Order
For some reason, Steam doesn't seem to like launching games if the game's proton is already being used, so you need to launch Elite Dangerous before you launch MetaElite 

## Font issues
Most people, if not everyone, has had fonts be rendered incorrectly. The font should look orange, like this:
<img width="372" height="182" alt="image" src="https://github.com/user-attachments/assets/b9297569-f262-489b-8119-a9acbf5240eb" />

however for you, it's probably white. That's ok, you can fix it with one command! Just run `protontricks 359320 settings fontsmooth=rgb`


# Overlay issues
First off, the overlay is not necessary to use MetaElite. It is cool and nice to have though. Not everyone has had issues with the overlay, but some people have. I wrote a script that will hopefully fix it for you. The script only works on x11 however, not Wayland. If you're not sure which you're on, the command `echo $XDG_SESSION_TYPE` should tell you. If you're on Wayland and are having trouble with the overlay, reach out in the #linux channel on the [MetaElite Discord](https://discord.com/invite/BnMdzncKW6).

First off, the script requires [xdotool](https://github.com/jordansissel/xdotool). Here's the command to install it for several distributions:
- Debian and Ubuntu: `apt-get install xdotool`
- Fedora: `dnf install xdotool`
- OpenSUSE: `zypper install xdotool`
- Arch: `pacman -S xdotool`

Once it's installed, edit the script you made earlier to be the following instead:
```bash
#!/usr/bin/env bash

elite_dangerous_id=359320
pfx="$(protontricks --command 'echo $WINEPREFIX' "$elite_dangerous_id" 2>/dev/null)"
cd "$pfx/drive_c/Program Files/raxxla.org/MetaElite" || exit
protontricks-launch --appid "$elite_dangerous_id" ./MetaElite*.exe >/dev/null 2>&1 &

# I (Zoe) figure 120 is plenty enough time to download a potential update
# and for MetaElite to do all it's launching things.
# If it isn't, you can change the number.
# If you launch and close MetaElite multiple times within the timeframe,
# and the overlay wasn't on,
# there may be multiple versions of the script running until the time passes.
# This shouldn't cause much of an issue,
# worst case scenario I think is the overlay maybe flickering a bit at first when you turn it on,
# and slightly wasted computing power,
# but this isn't exactly a computationally heavy script.
starttime="$(date +%s)"
maxtime=120
has_seen_overlay=false

overlay_needs_patch=true # If the overlay is "patched" over and over again, it will flicker
while true; do
    if ! xdotool search --name '^MetaElite$' > /dev/null && ( (( "$(date +%s)" - starttime > maxtime )) || $has_seen_overlay ); then 
        echo "MetaLens has been closed. Shutting down script"
        exit
    fi
    id="$(xdotool search --name "^MetaLens Overlay$")"
    if [ -n "$id" ]; then
        has_seen_overlay=true
        if $overlay_needs_patch; then
            # https://unix.stackexchange.com/a/680848/
            if xdotool set_window --overrideredirect 1 "$id"; then
                echo "Step 1/3 of patch complete";
            else
                # Sometimes I think xdotool detects it before the window is fully set up. I think 0.2 seconds is enough time for it to finish, and also it's short enough that issues caused by an unpatched overlay is minimal
                sleep 0.2
                continue
            fi
            xdotool windowunmap "$id" && echo "Step 2/3 of patch complete"
            xdotool windowmap "$id" && echo "Step 3/3 of patch complete"
            overlay_needs_patch=false
        fi
    else
        echo "Overlay is off"
        overlay_needs_patch=true
    fi
    # This loop will run every 0.1 seconds while MetaLens/MetaElite is open,
    # that way you can toggle the overlay all you like,
    # and it should fix itself within 0.1 seconds.
    # It should hopefully not impact performance much
    sleep 0.1
done | uniq # uniq is so it doesn't print "Overlay is off" over and over and over again while the overlay is off
```

# pipx
If you're using [pipx](https://github.com/pypa/pipx) to install protontricks, you'll want to make sure `/home/YOURUSERNAME/.local/bin` is in PATH environment variable. Or you can just replace all uses of protontricks-launch with `/home/YOURUSERNAME/.local/bin/protontricks-launch` I guess. pipx will talk about putting it in the $PATH in your `.bashrc` file, but that won't be sufficient since the script will not load your `.bashrc` file. The best way I've found to guarantee that it's in your path is to edit `/etc/profile`. You can just add the following line to it: `export PATH="$PATH:/home/YOURUSERNAME/.local/bin` (replace YOURUSERNAME with your username. If you don't know what is is, run the command `whoami`)
