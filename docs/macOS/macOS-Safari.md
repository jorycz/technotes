# macOS Safari

## Clean DNS cache in Safari

* **Safari** > Preferences > Advanced and check "**Show develop menu in menu bar**"  
* Develop > Empty Caches (shortcut is ⌥⌘E)  
* (Restart of Safari may be needed)

## Safari Tabs Rescue

Backup SQLite DB `SafariTabs*` and `CloudTabs*`

* Path depending on macOS version
* * `/Users/${USER}/Library/Containers/Safari/Data/Library/Safari/`
* * `/Users/${USER}/Library/Containers/com.apple.Safari/Data/Library/Safari/`

`History.db*` is in `/Users/${USER}/Library/Safari/`

If you have TimeMachine backup, you can try to restore from it, but it doesn't work for me.

`/Users/${USER}/Library/Containers/com.apple.Safari/Data/Library/Saved Application State/`
