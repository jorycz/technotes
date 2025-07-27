# macOS - iTerm2

## Installation

* [iTerm2 Website](https://www.iterm2.com/)
* or `brew install --cask iTerm2`

## Install font SF Mono

* Open all `SF-Mono...` fonts using Finder from system Terminal app at `/System/Applications/Utilities/Terminal.app/Contents/Resources/Fonts/` in Font Book and Install them.
* Or download them from [Apple Fonts Page](https://developer.apple.com/fonts/)

## Configuration

`tmux` settings make sense if using SSH and something like `tmux -CC new -A -s git1`

* General - tmux
* * Enable `Automatically bury the tmux client session after connecting` which will hide window with tmux session with control channel and show only iTerm2 terminal.
* * Disable `Use "tmux" profile rather than profile of the connecting session` to use default profile settings

* Profiles (Default)
* * Colors
* * * Background - `000000`
* * * Foreground - `ffffff`
* * * Red - `c74d35`
* * * Blue - `5369f9`
* * Text
* * * Thin strokes `On Retina Displays` should be enabled by default
* * * Font - `SF Mono` - `Light` - `16` - 100 - 100
* * * `Anti-aliased` should be enabled by default
* * Window
* * * Transparency - `5`
* * Terminal
* * * Scrollback lines: `10000`
* * Keys
* * * Key Bindings
* * * * In `Presets...` choose `Natural Text Editing` for default macOS keys navigation
