# Linux and Docker 

## PART ONE (one hour)

### Linux OS and file system

- The Operating system is the intermediary between the applications and the hardware running on your local machine
- An operating system can have a GUI or NOT (servers usually don't have GUI)
- Each OS takes up RAM, CPU & Storage
- RAM is used for running processes, if you run out, there's SWAP which turns disk storage into RAM
- CPU is running one application at a time (dual core or quad core)
- Storage is the files stored in directories
- Operating system has a kernel which manages the CPU, RAM, Storage
- Variation of the kernel is the distro (ubutnu, debian, redhat, fedora, etc.)
- POSIX
- GNU/Linux
- 

**TASK**
- Using commands inside the termianl, Find out what operating system you are running
- 


- NOTE: you don't interact with these regularly, the package manager or executable will automatically store files in various locations for you
- There are hidden files that you don't see (start with a .)

```bash
/
/home # home directories and files from users
/bin # user command binaries (cat, echo, etc.)
/sbin # system binaries (need sudo)
/usr # user 
/usr/bin # primary executables that system needs in order to run
/usr/local # programs installed just for that user
/lib # when executables in /bin need additional library files in order to run
/var # variable data (temporary)
/var/log # logs are stored here usually for 30 days
/var/log/syslog # system logs
/var/cache # cached data from programs
/opt # programs that install everything in one direcotry (not separated in /bin and /lib)
/etc # system wide configurations
/etc/fstab # controls how different filesystems are treated each time they are introduced to a system
/etc/hosts # used to translate hostnames to IP-addresses. 
/etc/hostname # name of the machine
/etc/sudoers # who can act as the super user (sudo)
/tmp # temporary location for running processes
/boot # do not touch - for booting the system
/dev # devices configurations like mouse and keyboard
/media # devices like CD or usb drive auto mounts here
/mnt # temporary mount points for additional filesystems

```

### Basic Linux Commands

```bash
# man pages for everything
man curl
man ping
man grep
man ls

# present working directory
pwd

# use bash shell
bash

# what shell am I running
# bash is command language interpreter
echo $0
echo $SHELL
cat /etc/shells

# to user home dir
cd
cd ..
cd /usr/local/bin
cd ../..

# create directory
mkdir mynewdirectory
mkdir -p mynewdirectory

# move or rename
mv mynewdirectory mydir

# copy
cp -r mydir mydir2

# delete directory
rm -r mydir2

# create file
touch file1.txt
touch file1.txt file3.txt
mkdir -p dir1
touch dir1/{file4.txt,file3.txt}

# cat file
cat file1.txt

# copy
cp file1.txt file2.txt

# delete file
rm file1.txt

# clear the terminal
clear

# ls
ls
ls -al
ls -R /home

# change ownership of file
chown chad:root file1.txt

# change just the user
chown chad file1.txt

# change just the group
chgrp root file1.txt

# take away write permissions for the group
chmod g-w file1.txt

# give executable permission to the group
chmod g+x file1.txt

# give user permission to execute
chmod u+x script.sh

# give other permissions to execute
chmod o+x script.sh

# give executable for all
chmod +x script.sh

# pipe to less
cat /var/log/syslog | less

cat /var/log/syslog | grep file

cat /var/log/syslog | grep ubuntu | less

# pipe to a file
history | grep ls > commands.txt
history | grep history >> commands.txt

# alias
alias
alias k=kubectl
alias d=docker

# find what user you are logged in as
whoami
echo $USER

hostname

# find the release info
lsb_release -a
cat /etc/os-release
uname -a
lscpu
lsmem

# go to previous command up and down arrows
# use reverse search with cmd + R
# also see all history
history
history 10

groups 

# default options for new users
useradd -D

# change from Bourne shell to Bourne Again SHell
useradd -D -s /bin/bash

useradd chad
adduser chad # will prompt for password and other info

# create user and add a home directory
sudo useradd -m chad
sudo useradd -m newuser

# Add User to Sudo Group
usermod -aG sudo chad

# add group
sudo groupadd docker
sudo addgroup devops
getent group devops

# add your user to the docker group
usermod -aG docker $USER

# delete user
userdel chad

# delete user from group
gpasswd -d chad devops

# reset password with passwd
passwd chad

# what groups user is in
groups chad
cat /etc/group

# get numeric ID’s (UID or group ID) of the current user
id
id root

# You can find the UID in the /etc/passwd file
cat /etc/passwd

# from left to right
# Name, Password (indicated with the letter (x)), UID (User ID), GID (Group ID), Gecos – Contain general information about the user and can be empty, Home directory, Shell – The path to the default shell for the user.

# A UID (user identifier) is a number assigned by Linux to each user on the system. This number is used to identify the user to the system and to determine which system resources the user can access.
# UID 0 (zero) is reserved for the root.
# Groups in Linux are defined by GIDs (group IDs).
# GID 0 (zero) is reserved for the root group.


# change user
su chad
su root

vim .bashrc
PS1="MyCustomPrompt $ "
source .bashrc

# bash history contains all command history

vim /etc/sudoers
sudo !!

# ENVIRONMENT VARIABLES
APP_VERSION=v2

# view all environment variables
env
printenv

# update packages
# package manager helps with installing software and all dependencies
apt update
apt search python
apt install -y python3
apt remove -y python3

# docker pre-requisites 
apt update && apt install -y ca-certificates \
curl \
gnupg \
lsb-release

# GPG, or GNU Privacy Guard, is a public key cryptography implementation. This allows for the secure transmission of information between parties and can be used to verify that the origin of a message is genuine
# apt-key is a program that is used to manage a keyring of gpg keys for secure apt.
mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# When installing packages using apt the packages are downloaded from one or more apt software repositories. 
# An APT repository is a network server or a local directory containing deb packages and metadata files that are readable by the APT tools.
# While there are thousands of application available in the default Ubuntu repositories, sometimes you may need to install software from a 3rd party repository.

# add docker 3rd party repository
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# pull down packages locally
apt search docker-ce
apt update
apt search docker-ce

# install  Docker Engine, containerd, and Docker Compose
apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# show docker-ce package metadata from repository
# madison is the name of the Debian archive management tool used for all debian and debian-like distros
apt-cache madison docker-ce

# install a specific version
apt install -y docker-ce=5:20.10.17~3-0~ubuntu-focal docker-ce-cli=5:20.10.17~3-0~ubuntu-focal containerd.io docker-compose-plugin

# verify its all working
docker run hello-world

# On Ubuntu and all other Debian based distributions, the apt software repositories are defined in the /etc/apt/sources.list file or in separate files under the /etc/apt/sources.list.d/ directory.
# The names of the repository files inside the /etc/apt/sources.list.d/ directory must end with .list.
cat /etc/apt/sources.list
ls /etc/apt/ # see sources.list.d

```

### Package managers 

### Working with VIM native text editor
- command mode
- insert mode

```bash
:q  # quit
:w  # write
:wq # write and quit
:q! # quit without saving
:wq! # write and quit by force
command + ^ # beginning of line
command + $ # end of line
gg   # top of file
G    # bottom of file (last line)
i	 # Insert at cursor (goes into insert mode)
A	 # Write at the end of line (goes into insert mode)
ESC	 # get out of insert mode
u	 # Undo last change
U	 # Undo all changes to the entire line
o	 # Open a new line (goes into insert mode)
dd	 # Delete line
D	 # Delete contents of line after the cursor
C	 # Delete contents of a line after the cursor and insert new text. Press ESC key to end insertion.
dw	 # Delete word
4dw	 # Delete 4 words
cw	# Change word
~	# Change case of individual character
:set nu # view line numbers
:set paste # paste without auto-indent
:set nopaste # paste with auto-indent

tail -5 $HOME/.bashrc
tail -5 /etc/passwd
head -5 $HOME/.bashrc
head -5 /etc/passwd


```


### file and directory permissions 
## Linux pipes and redirects 

### linux system services

```bash
# running processes by user
ps -u chad | grep firefox

# find process for firefox
pgrep firefox

# kill the firefox process (after running previous command)
kill 2376 # default SIGTERM

# see all kill signals
kill -l

# stop a process (SIGSTOP)
kill -19 2376

# interrupt a process (SIGINT)
kill -2 2376

# kill forcefully (SIGKILL)
kill -9 2376

# kill all the proccesses by name (SIGKILL)
pkill -9 firefox

# see running processes by cpu usage
top

# see running processes by cpu
htop

# stop a foreground process
ping -c 1000 google.com # hit enter, then while running press ctrl + z

# view the foreground process you stopped
jobs

# change foreground process into backgrond process
bg 1 # where 1 is the job number (from jobs command)

# change background process into a foreground process
fg 1 # where 1 is the job number (from jobs command)

# start a background process
ping -c 300 hackthebox.eu &

# running processes form all users
ps -aux

# process tree with systemd at the top
pstree

# systemd process is process #1
ps -aux | grep systemd

# stop ssh service
sudo systemctl stop sshd

# ssh status 
sudo systemctl status sshd

# start ssh service
sudo systemctl start sshd

# restart sshd
sudo systemctl restart sshd

# reload 
sudo systemctl reload sshd

# reload or restart (let system chose)
sudo systemctl reload-or-restart sshd

# stop ntp when system boots
sudo systemctl disable ntp

# start when system boots up
sudo systemctl enable ntp

# if ntp daemon is active
sudo systemctl is-active ntp

# check if enabled
sudo systemctl is-enabled ntp

# list active services
sudo systemctl list-units

# list only service type daemons
sudo systemctl list-units -t service

# list active and inactive daemons
sudo systemctl list-units --all

# list daemons NOT loaded into memory
sudo systemctl list-unit-files | grep nginx

# get status of nginx
sudo systemctl status nginx

# enable nginx
sudo systemctl enable nginx

# check if nginx is enabled
sudo systemctl is-enabled nginx

# start nginx
sudo systemctl start nginx

# check status of nginx.service (full name)
sudo systemctl status nginx.service

# check logs for nginx
sudo journalctl -xe

# check for journalctl unit (daemon)
sudo systemctl list-units | grep journal

# restart journald daemon
sudo systemctl restart systemd-journald

```

### Virtualization & Virtual machines

- you can install a hypervisor on your desktop/laptop (type 2 hypervisor)
- you can remove the OS layer and use ESXi or Hyper-V (type 1 hypervisor)
- 

### version control and git basics


```bash
# Configuring user information used across all local repositories
git config --global user.name “[firstname lastname]”

# set an email address that will be associated with each history marker
git config --global user.email “[valid-email]”

# initialize an existing directory as a Git repository
git init

# retrieve an entire repository from a hosted location via URL
git clone [url]

# show modified files in working directory, staged for your next commit
git status

# add a file as it looks now to your next commit (stage)
git add [file]

# unstage a file while retaining the changes in working directory
git reset [file]

# diff of what is changed but not staged
git diff

# diff of what is staged but not yet commited
git diff --staged

# commit your staged content as a new commit snapshot
git commit -m “[descriptive message]”

# list your branches. a * will appear next to the currently active branch
git branch

# create a new branch at the current commit
git branch [branch-name]

# switch to another branch and check it out into your working directory
git checkout

# create branch and switch to it
git checkout -b newbranch

# merge the specified branch’s history into the current one
git merge newbranch

# show all commits in the current branch’s history
git log

# show the commit history for the currently active branch
git log

# show the commits on branchA that are not on branchB
git log branchB..branchA

# show the commits that changed file, even across renames
git log --follow [file]

# show the diff of what is in branchA that is not in branchB
git diff branchB...branchA

# show any object in Git in human-readable format
git show [SHA]

# delete the file from project and stage the removal for commit
git rm [file]

# change an existing file path and stage the move
git mv [existing-path] [new-path]

# add a git URL as an alias
git remote add origin [url]

# fetch down all the branches from that Git remote
git fetch origin

# merge a remote branch into your current branch to bring it up to date
git merge origin/main

# Transmit local branch commits to the remote repository branch
git push origin main

# fetch and merge any commits from the tracking remote branch
git pull

# apply any commits of current branch ahead of specified one
git rebase [branch]

# clear staging area, rewrite working tree from specified commit
git reset --hard [commit]

# Save modified and staged changes
git stash

# list stack-order of stashed file changes
git stash list

# write working from top of stash stack
git stash pop

# discard the changes from top of stash stack
git stash drop


```


### branches, merging, rebase, stash, etc
### git history
### github: push, pull, commit in git
### using git for DevOps: infra as code

## PART TWO (two hours)


### Docker and container basics 

```bash
# change directory to cgroups system listing
cd /sys/fs/cgroup

# look at memory cgroup
ls memory/

# create a new cgroup and assign a process to it
mkdir memory/chd
ls memory/chad/

# compare memory cgroups before and after starting a new container with runc
lscgroup memory:/ > before.memory


```

### container networking and storage
### Dockerfile
### Docker compose 
### docker hub and container registries
### Push an image to an image repository 


This is intended to be a few hours of workshop where the expectation is to teach linux required for devops - basics commands etc and then moving to VMs' then moving to Docker and then explaing about dokcer , what it is container, installation, main commands, volumes, dockerfile, docker-compose, dockerhub, docker registry,

Yeah so basically we need to asume that the attendees have no prior knowledge and you make them understand in detail

when you reach the section on how containers work you can refer this video that I made - https://youtu.be/buHPsFgpsgU

similar you can find on dockerfile examples. 

Docker volume and security is missing which is part of docker 101 

Now there is no limit to the timings tbh so it totally depends on you which sections you think you can fast forward and which ones you can explain a bit well. I would say minimum 3 hours would be required as docker itself is a huge topic.

You can see this all needs to be covered for docker:
https://youtube.com/playlist?list=PL5uLNcv9SibBZj30yqG01a7A4_MXSyGK3

1 hr for linux and git  2 hour for docker IMO 
If I were you I would skip shell scirpt

https://iximiuz.com/en/posts/container-learning-path/