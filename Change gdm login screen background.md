## Warning

**This fork was made specifically to work with Ubuntu or Pop OS 22.04 aka Jammy!**
**DO NOT RUN THIS ON OTHER VERSIONS!**

If you are going to set an image file that has spaces in its file name or folders, remember to
scape them with backslashes.

## Installation

First, you will need to install libglib2.0-dev-bin:
```
sudo apt install libglib2.0-dev-bin
```
Then, you can download the script with the command below:
```
wget github.com/Chillsmeit/jammy-change-gdm-background/raw/master/jammy-change-gdm-background
```
And set it as an executable with:
```
chmod +x jammy-change-gdm-background
```

## Usage

Run the script with root privileges: 
```
sudo ./jammy-change-gdm-background /path/to/my/image
```

If you see a message `login image sucessfully changed`, then, when you restart gdm or reboot your
computer, your gdm background should be covered with the image you selected.

You can restore your original gdm theme any time with:
```
sudo ./jammy-change-gdm-background --restore
```

### Change Color

Now you can change that annoying purple color in Ubuntu or Grey in Pop_OS! to any color you like. <br>
Just type: 
```
sudo ./jammy-change-gdm-background \#yourhexcode
```
Your color hex format should have six characters like \\#407294 or three characters like \\#6ac.

### Multi-screen support

if you use two or more monitors you may see a streched image through the displays as if they were
    one screen. This is the default behavior of GDM3 when dealing with multiple displays.
