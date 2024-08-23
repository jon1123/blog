---
title: "My_Linux_VM_Setup"
date: 2024-08-23T16:09:04-07:00
draft: false
---

# Setting up an environment, on Linux

Hello this is the post about the steps I am currently following to make a New Linux VM attack box.

Most of this is from Hack The Box Academy - [Setting Up](https://academy.hackthebox.com/module/details/87). My HTB-Academy [Referral link](https://referral.hackthebox.com/mzwLTXe). Some of this is from other sources or I added when I found I keep going back to a tool not on the list. I don't currently have my own Windows set up. 
 
```bash
sudo apt update -y && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean -y
```

```bash
cat tools.list

ncat
nmap
wireshark
tcpdump
hashcat
ffuf
gobuster
hydra
zaproxy
proxychains
sqlmap
metasploit-framework
python3
theharvester
remmina
xfreerdp
rdesktop
exiftool
curl
seclists
testssl.sh
git
vim
tmux
peek
flameshot
ftp
exploitdb
locate
dnsrecon
espeak
imagemagick
dh-python
cifs-utils
gdb
whois
seclists

```

```bash
sudo apt install $(cat tools.list | tr "\n" " ") -y
```

```bash
cd /opt
sudo git clone https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite.git

ls -l privilege-escalation-awesome-scripts-suite/

### First try: sudo apt install seclists
### sudo git clone https://github.com/danielmiessler/SecLists.git

sudo git clone https://github.com/tmux-plugins/tmux-logging ~/tmux-logging

sudo mkdir plugins
sudo wget -O ~/.gdbinit-gef.py -q https://gef.blah.cat/py
sudo echo source ~/.gdbinit-gef.py >> ~/.gdbinit
```

```bash
# tmux.conf  -- ippsec + my changes

# Remap prefix to screens
set -g prefix C-a
bind C-a send-prefix
unbind C-b

# Quality of life stuff
set -g history-limit 10000
set -g allow-rename off

# other things I  am trying
set-option -g mouse on
setw -g mode-keys vi
set-option -s set-clipboard off
bind P paste-buffer
bind-key -T copy-mode-vi v send-keys -X begin-selection
bind-key -T copy-mode-vi y send-keys -X rectangle-toggle
unbind -T copy-mode-vi Enter
bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel 'xclip -se c -i'
bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel 'xclip -se c -i'


## Join Windows
bind-key j command-prompt -p "join pane from:" "join-pain -s '%%'"
bind-key s command-prompt -p "send pane to:" "join-pain -t '%%'"

# Search Mode VI (default is emac)
set-window-option -g mode-keys vi

run-shell /opt/tmux-logging/logging.tmux

```

```bash
#!/bin/bash

#### Make a backup of the .bashrc file
cp ~/.bashrc ~/.bashrc.bak

#### Customize bash prompt
echo 'export PS1="-[\[$(tput sgr0)\]\[\033[38;5;10m\]\d\[$(tput sgr0)\]-\[$(tput sgr0)\]\[\033[38;5;10m\]\t\[$(tput sgr0)\]]-[\[$(tput sgr0)\]\[\033[38;5;214m\]\u\[$(tput sgr0)\]@\[$(tput sgr0)\]\[\033[38;5;196m\]\h\[$(tput sgr0)\]]-\n-[\[$(tput sgr0)\]\[\033[38;5;33m\]\w\[$(tput sgr0)\]]\\$ \[$(tput sgr0)\]"' >> ~/.bashrc

##### Also add to .bashrc
function extract {
  if [ -z "$1" ]; then
    echo "Usage: extract <path/file_name>.<zip|rar|bz2|gz|tar|tbz2|tgz|Z|7z|xz|ex|tar.bz2|tar.gz|tar.xz>"
  else
    if [ -f $1 ]; then
      case $1 in
        *.tar.bz2)   tar xvjf $1    ;;
        *.tar.gz)    tar xvzf $1    ;;
        *.tar.xz)    tar xvJf $1    ;;
        *.lzma)      unlzma $1      ;;
        *.bz2)       bunzip2 $1     ;;
        *.rar)       unrar x -ad $1 ;;
        *.gz)        gunzip $1      ;;
        *.tar)       tar xvf $1     ;;
        *.tbz2)      tar xvjf $1    ;;
        *.tgz)       tar xvzf $1    ;;
        *.zip)       unzip $1       ;;
        *.Z)         uncompress $1  ;;
        *.7z)        7z x $1        ;;
        *.xz)        unxz $1        ;;
        *.exe)       cabextract $1  ;;
        *)           echo "extract: '$1' - unknown archive method" ;;
      esac
    else
      echo "$1 - file does not exist"
    fi
  fi
}

```

```sh
cp ~/.vimrc ~/.vimrc.bck

### add to .vimrc
syntax on
set tabstop=4
filetype on
set nu
set ruler
set mouse=a
set list

```
looking to add but have not tested yet
[subfinder](https://github.com/projectdiscovery/subfinder) 
[Caido](https://caido.io/download) to replace burpsuite
[llm](https://github.com/simonw/llm) this is a cli large language modal api I am thinking of selfhosting this on a local server so not need to recreate it every time. 
[chepy](https://github.com/securisec/chepy) this is cli cyber chef
```sh 
git clone --recursive https://github.com/securisec/chepy.git
cd chepy
pip3 install -e .
# I use -e here so that if I update later with git pull, I dont have it install it again (unless dependencies have changed)
```

note - I need to look into a Vertual Server 

