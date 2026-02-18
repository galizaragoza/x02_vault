# Fixes
## Cursor invisible
1. Open a terminal.

2. Create a small Xorg configuration override:

```bash
sudo mkdir -p /etc/X11/xorg.conf.d
sudo nano /etc/X11/xorg.conf.d/20-vmware.conf
```

3. Paste this inside:

```
Section "Device"
    Identifier "VMware SVGA"
    Driver "vmware"
    Option "HWCursor" "off"
EndSection
```

4. Save and close (Ctrl + O, Enter, Ctrl + X).

5. Reboot:

```
sudo reboot
```

Shoutout to the fucking legend [source](https://www.reddit.com/r/Kalilinux/comments/1o0mxon/cursor_invisible_after_updates/)
