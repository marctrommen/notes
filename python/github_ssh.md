---
title: "GitHub per SSH-Key einrichten und erreichen"
---

[← Zurück zu Python](./)

[← Zurück zur Startseite](../)

# GitHub per SSH-Key einrichten und erreichen

*	SSH CLI apps location on Windows Systems

	`C:\Windows\System32\OpenSSH`

*	location of SSH keys on Windows Systems

	`C:\Users\%USERNAME%\.ssh`

*	`C:\Users\%USERNAME%\.ssh\known_hosts`

*	create SSH key pair für GitHub:

	run in Terminal:
	
	`ssh-keygen -t ed25519 -C "your_email@example.com"`
	
	example
	
	```bat
	ssh-keygen -t ed25519 -C "peter.pan@gmx.aut"
	```
	
	```
	C:\Users\marcu>ssh-keygen -t ed25519 -C "marcus.trommen@gmx.net"
	Generating public/private ed25519 key pair.
	Enter file in which to save the key (C:\Users\marcu/.ssh/id_ed25519):
	Created directory 'C:\\Users\\marcu/.ssh'.
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
	Your identification has been saved in C:\Users\marcu/.ssh/id_ed25519
	Your public key has been saved in C:\Users\marcu/.ssh/id_ed25519.pub
	The key fingerprint is:
	SHA256:xZURojfPqeU2zCbKxf/plR9/FR5XSdiwskfmzldyr1A peter.pan@gmx.aut
	The key's randomart image is:
	+--[ED25519 256]--+
	|          . ++ o.|
	|         o o. . o|
	|        . =  . o.|
	|         o + .+o=|
	|        S   = EB+|
	|         . * .o.B|
	|          = O  O=|
	|       . o = o oX|
	|        o   .o=.=|
	+----[SHA256]-----+
	```
	
	generates with default values in folder
	`C:\Users\%USERNAME%\.ssh`
	
	following files:
	
	`id_ed25519` and `id_ed25519.pub`

*	Add SSH key to SSH-Agent

	see: https://docs.github.com/de/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
	oben PowerWhell Window with Admin rights and run following commands
	
	```bash
	# start the ssh-agent in the background
	Get-Service -Name ssh-agent | Set-Service -StartupType Manual
	Start-Service ssh-agent
	```
	
	add the generated key:
	
	```bash
	ssh-add c:/Users/YOU/.ssh/id_ed25519
	```
	
*	add the SSH Key to the GitHub account

	https://docs.github.com/de/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account
	
	Open in web browser https://github.com/settings/keys and add the public key

*	Test ssh connection to GitHub

	Open Windows terminal (`cmd`) and enter the command:
	
	```bash
	ssh -T git@github.com
	```
	
	```bash
	C:\Users\marcu>ssh -T git@github.com
	The authenticity of host 'github.com (140.82.121.3)' can't be established.
	ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
	This key is not known by any other names.
	Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
	Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
	```

*	create new GitHub Repo and connect it to an existing local Repo

	[Link to article](https://www.heise.de/ratgeber/Einstieg-in-Git-und-GitHub-Erste-Projekte-realisieren-6216127.html?seite=5)
	
	SSH: `git@github.com:marctrommen/my_project.git`
	
	```bash
	git remote add origin git@github.com:marctrommen/my_project.git
	git push
	```