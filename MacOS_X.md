MACOS X
=======

Install Homebrew

Prereq: Install Xcode et Xcode Commandline Tools

    ruby -e "$(curl -fsSkL raw.github.com/mxcl/homebrew/go)"

Install Git

    brew install git

Install Ruby 1.9

    brew install rbenv
    brew install ruby-build
    echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
    rbenv install 2.0.0-p247
    rbenv install 1.9.3-p448
    rbenv global 1.9.3-p448
    gem install rails
    rbenv rehash

Install Python

    brew install python

Install SSHFS

    brew install sshfs

Install Tmux

    brew install tmux

tmux -CC pour intégration avec iTerm2

Show hidden files in finder

    defaults write com.apple.finder AppleShowAllFiles TRUE
    killall finder

Disable/Enable Window animation

    defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false # or true

Show full path in finder

    defaults write com.apple.finder _FXShowPosixPathInTitle -bool true

Dock

Remove the Auto-Hide Delay:

    defaults write com.apple.Dock autohide-delay -float 0 && killall Dock
    defaults delete com.apple.Dock autohide-delay && killall Dock # for restore

Remove Dock Animation Completely:

    defaults write com.apple.dock autohide-time-modifier -int 0;killall Dock

Set a Fast Dock Display Animation:

    defaults write com.apple.dock autohide-time-modifier -float 0.12;killall Dock

Restore Default Animation Speed.

    defaults delete com.apple.dock autohide-time-modifier;killall Dock

Keepassx

Open multiple instances:

    open -n /Applications/KeePassX.app /file/to/database.kdb

Finder Quick Look

Enable text selection:

    defaults write com.apple.finder QLEnableTextSelection -bool TRUE; killall Finder

Disable startup sound

    sudo nvram SystemAudioVolume=%80

Enable:

    sudo nvram -d SystemAudioVolume

Enable Single Application Mode

    defaults write com.apple.dock single-app -bool true
    killall Dock

Copy Text from Quick Look

Quick Look is one of OS X's best features. Just hit the Spacebar when you select a file, and you'll see a preview of that file without having to open up an app. It's great, but you can't select any text when you're in the preview. You can add that feature with a Terminal command

    defaults write com.apple.finder QLEnableTextSelection -bool true
    killall Finder

Add Pop-Up Notifications to iTunesP

Want to get a little notification of the currently playing track in iTunes right from the app's icon? It's easy

    defaults write com.apple.dock itunes-notifications -bool TRUE
    killall Dock

If you're not a fan, disabling it just requires a simple command as well

    defaults delete com.apple.dock itunes-notifications

Get Rid of the Delay When Showing and Hiding the Dock

By default, the Dock has a slight delay when you mouse over it and have "Automatically Hide and Show Dock" enabled. It's a little annoying, but you can customize the speed of that animation with this command

    defaults write com.apple.Dock autohide-delay -float 0 &&
    killall Dock

Stop Quicktime and Preview from Auto-Restoring What You Left Open

Auto-restore is a handy setting that opens up your last used file in your apps. That's fine for most apps, but it could potentially lead to some embarrassing things in an app like Quicktime or Preview. Thankfully, it's easy to disable that feature on an app-by-app basis

For Preview:

    defaults write com.apple.Preview NSQuitAlwaysKeepsWindows -bool false

For Quicktime:

    defaults write com.apple.QuickTimePlayerX NSQuitAlwaysKeepsWindows -bool false

Disable Window Animations to Speed Up an Older Mac

The window animations in OS X are great, but they'll slow down old Mac's quite a bit. If you want to get a little more speed out of your operating system, just disable them

    defaults write NSGlobalDomain NSAutomaticWindowAnimationsEnabled -bool false

Prevent Your Mac from Sleeping

Your computer goes to sleep so that it doesn't waste resources and power working when you're not using it, but sometimes you need to temporarily keep it awake while you're doing a certain task. To do this, just enter this command into Terminal:

    caffeinate -t 3600

That will keep you Mac awake for an hour. The 3600 comes from how many seconds are in an hour, so just change that number to however long you want to keep your computer awake for.
