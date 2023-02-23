## <p align="center">Custom Server Hardening Script</p>


# About

A lot of good virtual servers get destroyed every year because they are hacked by evildoers that take advantage of unpatched exploits and simple passwords. It's frustrating that many of these hacks could have been prevented by some basic hardening. With large numbers of Linux newcomers flocking to enter the space and set up masternodes I saw a need for a simple way to secure virtual servers that would, *for the most part*, keep the bad guys out. It is my intent to keep this script general enough that it could be the first script run on many different types of VPS installations to prepare them for whatever use you have planned.

The following is a list of different sections of the script, which are broken up into separate functions which are called from the very end of the script. 

1. CREATE SWAP / if no swap exists, set swap to 2x RAM (not less than 2GB)
2. UPDATE AND UPGRADE / update operating system & pkgs
3. INSTALL FAVORED PACKAGES / useful tools & utilities
4. INSTALL CRYPTO PACKAGES / common crypto packages
5. USER SETUP / add new sudo user, copy SSH keys
6. SSH CONFIG / change SSH port, disable root login
7. UFW CONFIG / UFW - add rules, harden, enable firewall
8. HARDENING / before rules, secure shared memory, etc
9. GOOGLE AUTH / enable 2fa using Google Authenticator
10. KSPLICE INSTALL / automatically update without reboot
11. MOTD EDIT / replace boring banner with customized one
12. RESTART SSHD / apply settings by restarting systemctl
13. INSTALL COMPLETE / display new SSH and login info

# Installation

SSH into your VPS and run this command to clone the Github repository and begin installation:

```bash
sudo git clone https://github.com/adamito/vps-harden.git && cd vps-harden && sudo bash get-hard.sh
```

The script will then walk you through each of the server hardening steps, offering prompts for feedback and other notes along the way. You really can't mess it up. I have tried.

<p align="center"><img src="/media/07. root login.png" alt="Root Login"></p>

The script will keep a log of changes that are made to the system which you can review when it is complete. The output will also be dispalyed on the screen.

<p align="center"><img src="/media/11. hardening rules.png" alt="Hardening Rules"></p>

When you are finished, you'll see confirmation that the script completed setup and you will be given a list of notes about your installation. The installation log file is stored in the server's log folder at `/var/log/server_hardening.log`

<p align="center"><img src="/media/15 install complete.png" alt="Install Complete"></p>

I would recommend that you take a screenshot of that page and save it for later. It has important information about your setup and if you don't keep note of the settings you entered, you could find yourself locked out of your server.  If you chose to require RSA key for login and disabled password login, be sure that you have an RSA key installed as described in the next section.

## (Optional) Post-Installation Hardening with RSA Key-pair

In order to secure your server's root login via SSH, you may follow these steps on your VPS:
```
mkdir ~/.ssh && touch ~/.ssh/authorized_keys
sudo chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
sudo nano ~/.ssh/authorized_keys
```
At this point you need to copy your RSA public key and paste it into the `authorized_keys` file and save the changes.  [Vultr's documentation](https://www.vultr.com/docs/how-do-i-generate-ssh-keys/) has a great guide on how to generate the key-pair. To activate the changes you need to restart the SSH service using this command: `sudo systemctl restart sshd`. Don't close out your existing session until you have made a test connection using your private key for authentication. If the connection works, it is now safe to edit the sshd_config using the command below to disable password authentication altgoether by changing the line to read “PasswordAuthentication no” and save the file save file.
```
sudo nano /etc/ssh/sshd_config
```

You will once more need to run `sudo systemctl restart sshd` to make those changes to sshd_config active and now your server will be secured using your RSA public/private key pair which is infinitely more secure than using a root password to login.


Additionally, there are some additional files you can modify to suit your needs. I have listed a few of these files below along with why you might consider editing them.


### SSH Configuration
/etc/ssh/sshd_config

### Login Banner
/etc/issue.net

### Automatic Update Settings
/etc/apt/apt.conf.d/10periodic<br/>
/etc/apt/apt.conf.d/50unattended-upgrades<br/>

### Ksplice Settings
/etc/uptrack/uptrack.conf

## Help, Issues and Questions

I have tried to troubleshoot the script for errors and confirmed that it works with a VPS you configure on [Vultr](https://www.vultr.com/?ref=7568060), 
[Digital Ocean](https://www.digitalocean.com/?refcode=bd6020302487), and [Be Your Own VPS](https://www.youtube.com/playlist?list=PLTblguczzdyajCPQGlpJjHUvSNV8WNsGQ). It works in all of these as long as you're using Ubuntu 16.04, 18.04, or 20.04 LTS. I have not tested it with anything but Ubuntu.
