---
layout: post
title:  "Ubuntu Setup"
date:   2021-03-05 23:30:31 +0800
categories: tips
---
**"sed" commands are for fresh start only**

**if you changed the target file before, use an editor**

# sudo NOPASSWD (dangerously fast root privilege)

    sudo visudo

add line (replace `USER` with current username)

    USER    ALL=(ALL)   NOPASSWD:ALL

save and exit

# ssh login (remote server)

on client side, generate SSH key pair for the first time

    ssh-keygen

on client side, send public key

    ssh-copy-id user@hostname

# set hostname (if allowed to)

*WSL copies Windows hostname at every boot-up*

backup

    sudo cp /etc/hostname /etc/hostname.backup

set new hostname

    sudo nano /etc/hostname

reboot (later) to update the hostname

# switch apt archive sources

replace `OLD_SOURCE` with `xx.archive.ubuntu.com` found in the `/etc/apt/sources.list`

replace `NEW_SOURCE` with `mirrors.XXX.XXX` [find yours here](https://launchpad.net/ubuntu/+archivemirrors)

    sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup

open vim

    sudo vim /etc/apt/sources.list

in vim

    :%s/OLD_SOURCE/NEW_SOURCE/g
    :x

upgrade packages

    sudo apt update && sudo apt upgrade -y

# shell setup

install zsh, curl, and git

    sudo apt install zsh curl git -y

set zsh as the default shell

    chsh -s /bin/zsh

install [oh-my-zsh](https://ohmyz.sh/) ([Gitee replca in China](https://gitee.com/mirrors/oh-my-zsh))

    sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

install [p10k](https://github.com/romkatv/powerlevel10k) (and [Font](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k))

    git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc`

install [zsh-autosuggestions & zsh-syntax-highlighting](https://gist.github.com/dogrocker/1efb8fd9427779c827058f873b94df95)

    git clone --depth=1 https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
    git clone --depth=1 https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
    sed -e s/"plugins=(git)"/"plugins=(git zsh-autosuggestions zsh-syntax-highlighting)"/g ~/.zshrc -i

# git setup

check config

    git config -l

add username and email

replace `GITHUB_FULLNAME` with the GitHub **FULL NAME** at *GitHub -> Account settings -> Profile -> Public profile -> Name*, but **NOT** the login username

replace `RANDOMNUMBER+GITHUBNAME@users.noreply.github.com` with the real email address or check *GitHub -> Account settings -> Emails -> Keep my email addresses private* and replace it with the email address below

    git config --global user.name "GITHUB_FULLNAME"
    git config --global user.email "RANDOMNUMBER+GITHUBNAME@users.noreply.github.com"

they will be in every git commit

[setup ssh key](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

# hyper-v resolution (if using [hyper-v](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/about/))

    sudo nano /etc/default/grub

edit one line

    GRUB_CMDLINE_LINUX_DEFAULT="quiet splash video=hyperv_fb:1920x1080"

generate config file and reboot

    sudo update-grub
    reboot

# WSL2 env

add Windows IP address (if WSL2 doesn't access Windows via localhost yet)

    export WSL_HOST=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')

add browser to env


    export BROWSER="/mnt/c/Program Files (x86)/Google/Chrome/Application/chrome.exe"

if you work with Jupyter Notebook

    jupyter notebook --generate-config
    sed -i "s/# c.NotebookApp.use_redirect_file = True/c.NotebookApp.use_redirect_file = False/g" ~/.jupyter/jupyter_notebook_config.py

if you work with Jupyter Lab

    jupyter lab --generate-config
    sed -i "s/# c.NotebookApp.use_redirect_file = True/c.NotebookApp.use_redirect_file = False/g" ~/.jupyter/jupyter_lab_config.py

**issue this repo on anything outdate*