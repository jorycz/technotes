# macOS Safari

## Clean DNS cache in Safari

* **Safari** > Preferences > Advanced and check "**Show develop menu in menu bar**"  
* Develop > Empty Caches (shortcut is ⌥⌘E)  
* (Restart of Safari may be needed)

## Safari Tabs Rescue

`cd ~/Library/Safari`

**Perform backup of files before you touch them.**

More records than in Safari menu `plutil -p RecentlyClosedTabs.plist | grep TabURL`

Inspect history `sqlite3 History.db`

    SELECT datetime(visit_time + 978307200, 'unixepoch', 'localtime'), url 
    FROM history_visits 
    JOIN history_items ON history_visits.history_item = history_items.id 
    ORDER BY visit_time DESC;

OR one-liner

    sqlite3 History.db "SELECT datetime(visit_time + 978307200, 'unixepoch', 'localtime'), url FROM history_visits JOIN history_items ON history_visits.history_item = history_items.id ORDER BY visit_time DESC;"

### Other tips

If you have TimeMachine backup, you can try to restore from it, but it doesn't work for me.

`/Users/${USER}/Library/Containers/com.apple.Safari/Data/Library/Saved Application State/`

Backup SQLite DB `SafariTabs*` and `CloudTabs*`

* Path depending on macOS version
* * `/Users/${USER}/Library/Containers/Safari/Data/Library/Safari/`
* * `/Users/${USER}/Library/Containers/com.apple.Safari/Data/Library/Safari/`
