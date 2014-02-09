LINUX UBUNTU
============

Package to install: `apt-get install open-ssh nmon vim nmap sysstat`

Remove old kernels:

    apt-get remove $(dpkg -l linux-image-3* | grep -v $(uname -r) | awk '/linux/ {print $2}')
    apt-get remove $(dpkg -l linux-headers-3* | grep -v $(uname -r) | awk '/linux/ {print $2}')

Install Sublime Text:

    sudo add-apt-repository ppa:webupd8team/sublime-text-2
    sudo apt-get update
    sudo apt-get install sublime-text
