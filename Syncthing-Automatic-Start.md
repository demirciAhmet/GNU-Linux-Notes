https://www.reddit.com/r/SteamDeck/comments/vocyi5/start_syncthing_automatically_on_steamdeck_even/

Next you will need a new text file located at /home/deck/.config/systemd/user/syncthing.service and the contents of the file will be 

```
[Unit]
Description=Syncthing

[Service]
Type=simple
ExecStart=flatpak run --command=syncthing me.kozec.syncthingtk
Restart=on-failure
RestartSec=1
SuccessExitStatus=3 4

[Install]
WantedBy=default.target
```
Then open the console and type:

```
systemctl --user enable syncthing

systemctl --user start syncthing
```

Now you should be able to open Syncthing GTK and it will no longer display the popup that it is starting a daemon because it is just connecting to the one already running. 

Now you can switch back to Game Mode and Syncthing will start automatically.

If you wish to disable Syncthing starting on launch you can run the following commands:

```
systemctl --user stop syncthing

systemctl --user disable syncthing
```
