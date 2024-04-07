GNOME Shell Extension Alt+Tab Scroll Workaround

    Quick fix to the bug where scrolling in one application is repeated in another when switching between them using Alt+Tab (e.g., VS Code and Chrome)

As an example, after scrolling VS Code and switching to Chrome using Alt+Tab, Super+Tab, Alt+Esc, or overview (hot corner or Super) + mouse click, the VS Code scroll would be repeated in Chrome. Installing the extension should avoid this.

GNOME 45 supported!

This bug is described in several open issues:

    microsoft/vscode#28795 (most commented VS Code bug!)
    pop-os/pop#2331
    atom/atom#15482
    GitLab: GNOME/mutter#401
    Chromium: chromium#608246
    Chromium: chromium#807187


# [Solution](https://github.com/pop-os/pop/issues/2331#issuecomment-1229371645)
```
I'm having this problem on Ubuntu since the upgrade to 22.04, and imwheel wasn't a nice solution for me because I use a notebook trackpad. I also tried other workarounds described at microsoft/vscode#28795, but any of them seemed good enough.

I created a GNOME Shell Extension as a temporary fix:

    Name: Alt+Tab Scroll Workaround
    Link: https://extensions.gnome.org/extension/5282/alttab-scroll-workaround/
    Code: https://github.com/lucasresck/gnome-shell-extension-alt-tab-scroll-workaround

Would love to see if it works for you too!
```
