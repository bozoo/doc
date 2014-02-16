GIT
===

Clone a git repo: `git clone https://github.com/$userName/$repoGit`

Clone a git repo in $repoDir: `git clone https://github.com/$userName/$repoGit $repoDir`

Save current working git: `git stash`

Configure proxy for git: `git config [--global] http.proxy http://user:pass@proxyhost:proxyport`

Remove sensitive data from git
------------------------------

Backup $pathToFileToRemove then:

    git rm $pathToFileToRemove
    git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch $pathToFileToRemove' --prune-empty --tag-name-filter cat -- --all
    git push origin master --force

Resolve a merge conflict

Keep remote file:

    rm $localConflictFile
    git checkout --theirs $remoteConflictFile
    fit commit -a

Keep local file:

    git rm $remoteConflictFile
    git add $localConflitFile
    git commit -a

Git config to access my github repo
-----------------------------------

    git config --global user.name "$userName"
    # Sets the default name for git to use when you commit
    git config --global user.email "$eMail"
    # Sets the default email for git to use when you commit
    git commit --amend --reset-author
    # Reset author informations for firsts commits
    git config --global credential.helper cache
    # Set git to use the credential memory cache
    git config --global credential.helper 'cache --timeout=3600'
    # Set the cache to timeout after 1 hour (setting is in seconds)

    mkdir -p github/$repoGit
    # Creates a directory for your project called "$repoGit" in your user directory
    cd github/$gitRepo/
    # Changes the current working directory to your newly created directory
    git init
    # Sets up the necessary Git files
    touch README
    # Creates a file called "README" in your $gitRepo directory
    git add README
    # Stages your README file, adding it to the list of files to be committed
    git commit -m 'first commit'
    # Commits your files, adding the message "first commit"
    git remote add origin https://github.com/$userName/$repoGit.git
    # Creates a remote named "origin" pointing at your GitHub repository


Git-achievement
---------------

    echo "PATH=$PATH:~/git/git-achievements" >> .bash_profile
    echo "alias git=git-achievements" >> .bash_profile
    git config --global achievement.upload "true"
    cd git-achievements/
    git symbolic-ref HEAD refs/heads/gh-pages
    rm .git/index
    git add .
    git commit -a -m "Hope it works"
    git push origin gh-pages

Install Git on CentOS 5
-----------------------

Git Server:

    yum install git git-daemon
    mkdir -p ${repoGit}.git

    cd !$
    git --bare init
    git update-server-info
    chown -R apache:apache .

    htpasswd -c /etc/httpd/conf/htpasswd-git git

    cat << EOF >> /etc/httpd/conf.d/git.conf
    SetEnv GIT_PROJECT_ROOT /data/nas/git
    SetEnv GIT_HTTP_EXPORT_ALL
    SetEnv REMOTE_USER=$REDIRECT_REMOTE_USER
    ScriptAlias /git/ /usr/libexec/git-core/git-http-backend/

    <Directory "/data/nas/git">
       Options None
       AllowOverride None
       Order allow,deny
       Allow from all
    </Directory>

    <Location /git>
       AuthType Basic
       AuthName "Git Access"
       AuthUserFile /etc/httpd/conf/htpasswd-git
       Require valid-user
    </Location>
    EOF

    service httpd restart

Git client:

    cd d:
    mkdir -p git/sysadmin
    cd !$
    git init
    git remote add origin http://${gitUser}@${gitServer}/${repoGit}.git
    touch README
    git add .
    git commit -a -m 'Initial import'
    git config --global user.name $userName
    git config --global user.email $userMail
    git commit --amend --reset-author
    git push origin master

Backup Git repos with Dropbox
-----------------------------

Storing Git repos in Dropbox synchronized can create corruption.
Git repos need to be stored outside Dropbox folder!

On the other hand, we can create a backup repos inside Dropbox folder.

Create a bare repository Git in dropbox folder:

    cd ~/Dropbox/REPOS
    git init --bare PROJET.git

It's important to use .git suffix for the repository name!

Go to the initial Git repos and add a remote for the backup repos:

    cd DIRPROJET
    git remote add dropbox ~/Dropbox/REPOS/PROJET.git

Push to the backup repos:

    cd DIRPROJET
    git push dropbox master

After each commit, push to the backup repos
