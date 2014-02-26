MACOS X
=======

Homebrew
--------

Install Xcode and Xcode Commandline Tools: `ruby -e "$(curl -fsSkL raw.github.com/mxcl/homebrew/go)"`

Install Git: `brew install git`

Install Ruby 1.9

    brew install rbenv
    brew install ruby-build
    echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
    rbenv install 2.0.0-p247
    rbenv install 1.9.3-p448
    rbenv global 1.9.3-p448
    gem install rails
    rbenv rehash

Install Python: `brew install python`

Install SSHFS: `brew install sshfs`

Install Tmux: `brew install tmux`

Qemu
----

Install prerequisites:

    brew install pkg-config glib
    brew link glib
    brew install apple-gcc42

Install Qemu:

    brew install qemu --env=std --use-gcc

iTerm2
------

Use solarized theme: https://github.com/altercation/solarized

Add an iTerm2 panel which can be called with "ALT+SPACE":
>Set HotKey Panel by selecting "Preferences" -> "Keys" -> "HotKey" and set iTerm2 to open with the session.

Oh-My-ZSH
---------

Install ZSH: `brew install zsh`

Install Oh-My-ZSH:

    git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
    cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
    chsh -s /bin/zsh

Install Oh-My-ZSH solarized theme:

    cd .oh-my-zsh/themes
    mv agnoster.zsh-theme agnoster.zsh-theme.bak
    wget https://gist.github.com/agnoster/3712874/raw/c3107c06c04fb42b0ca27b0a81b15854819969c6/agnoster.zsh-theme

Edit `.zshrc`: `ZSH_THEME="agnoster"`

Install Powerline fonts: `pip install --user git+git://github.com/Lokaltog/powerline`

Install patched Monaco for Powerline font:
 - `wget https://gist.github.com/baopham/1838072/raw/2c0e00770826e651d1e355962e751325edb0f1ee/Monaco%20for%20Powerline.otf`
 - Install it with double-click

Divers
------

Take a screenshot of the screen, and save it as a file on the desktop: `Command-Shift-3`

Take a screenshot of an area and save it as a file on the desktop: `Command-Shift-4`

Disable startup sound: `sudo nvram SystemAudioVolume=%80`

Enable startup sound: `sudo nvram -d SystemAudioVolume`

Enable Single Application Mode: `defaults write com.apple.dock single-app -bool true && killall Dock`

Disable Window Animations to Speed Up an Older Mac: `defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false`

Stop Quicktime and Preview from Auto-Restoring What You Left Open:

 - For Preview: `defaults write com.apple.Preview NSQuitAlwaysKeepsWindows -bool false`
 - For Quicktime: `defaults write com.apple.QuickTimePlayerX NSQuitAlwaysKeepsWindows -bool false`

Prevent Your Mac from Sleeping: `caffeinate -t 3600`

Use text completion:

1. Go to "System Preferences"
1. Select "Language and text", then select "Text"

Uses cases:
 - replace "@mail" by your email
 - replace "@name" by your name
 - replace "@phone" by your number

Insert selected text in a note: Select the text and press `Command + Shift + Y`

Copy from Terminal to MacOSX clipboard: `echo "test" | pbcopy`

Paste from MacOSX clipboard to Terminal: `pbpaste`

List all downloaded files on MacOSX: `sqlite3 ~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV* 'select LSQuarantineDataURLString from LSQuarantineEvent' | sort`

Delete downloaded files list: `sqlite3 ~/Library/Preferences/com.apple.LaunchServices.QuarantineEventsV* 'delete from LSQuarantineEvent'`

Finder
------

Show hidden files in finder: `defaults write com.apple.finder AppleShowAllFiles TRUE && killall finder`

Disable/Enable Window animation: `defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false # or true`

Show full path in finder: `defaults write com.apple.finder _FXShowPosixPathInTitle -bool true`

Disable .DSTORE file creation in network shares: `defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool true`

Move a file instead of copy: Press "Command" while moving the file with the mouse

Move many files in a new directory:

1. Select files
1. Open Contextual Menu
1. Select "New directory with the selection"

Show Library dir in Finder: `chflags nohidden ~/Library`

Finder Quick Look
-----------------

Enable text selection: `defaults write com.apple.finder QLEnableTextSelection -bool TRUE; killall Finder`

Copy Text from Quick Look: `defaults write com.apple.finder QLEnableTextSelection -bool true && killall Finder`

Dock
----

Remove the Auto-Hide Delay:

    defaults write com.apple.Dock autohide-delay -float 0 && killall Dock
    defaults delete com.apple.Dock autohide-delay && killall Dock # for restore

Remove Dock Animation Completely: `defaults write com.apple.dock autohide-time-modifier -int 0;killall Dock`

Set a Fast Dock Display Animation: `defaults write com.apple.dock autohide-time-modifier -float 0.12;killall Dock`

Restore Default Animation Speed: `defaults delete com.apple.dock autohide-time-modifier;killall Dock`

Get Rid of the Delay When Showing and Hiding the Dock: `defaults write com.apple.Dock autohide-delay -float 0 && killall Dock`

Mission Control
---------------

Change Mission Control screen:

1. Open file /System/Library/CoreServices/Dock.app
1. Go to folder Content/Resources
1. Rename file defaultdesktop.png to defaultdesktop-old.png

The default Desktop screen become Mission Control Screen

To choose the screen file:

1. Go to /System/Library/CoreServices/
1. Rename DefaultDesktop.jpg to DefaultDesktop-old.jpg
1. Replace file DefaultDesktop.jpg by your file.

Keepassx
--------

Open multiple instances: `open -n /Applications/KeePassX.app /file/to/database.kdb`

iTunes
------

Add Pop-Up Notifications to iTunes: `defaults write com.apple.dock itunes-notifications -bool TRUE && killall Dock`

Removing Pop-Up Notification to iTunes: `defaults delete com.apple.dock itunes-notifications`
