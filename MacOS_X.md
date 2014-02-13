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

tmux -CC for iTerm2 integration

Divers
------

Disable startup sound: `sudo nvram SystemAudioVolume=%80`

Enable startup sound: `sudo nvram -d SystemAudioVolume`

Enable Single Application Mode: `defaults write com.apple.dock single-app -bool true && killall Dock`

Disable Window Animations to Speed Up an Older Mac: `defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false`

Stop Quicktime and Preview from Auto-Restoring What You Left Open:

 - For Preview: `defaults write com.apple.Preview NSQuitAlwaysKeepsWindows -bool false`
 - For Quicktime: `defaults write com.apple.QuickTimePlayerX NSQuitAlwaysKeepsWindows -bool false`

Prevent Your Mac from Sleeping: `caffeinate -t 3600`

Finder
------

Show hidden files in finder: `defaults write com.apple.finder AppleShowAllFiles TRUE && killall finder`

Disable/Enable Window animation: `defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false # or true`

Show full path in finder: `defaults write com.apple.finder _FXShowPosixPathInTitle -bool true`

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

Keepassx
--------

Open multiple instances: `open -n /Applications/KeePassX.app /file/to/database.kdb`

iTunes
------

Add Pop-Up Notifications to iTunes: `defaults write com.apple.dock itunes-notifications -bool TRUE && killall Dock`

Removing Pop-Up Notification to iTunes: `defaults delete com.apple.dock itunes-notifications`
