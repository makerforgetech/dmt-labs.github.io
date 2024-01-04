---
layout: post
title: Local Development on Remote Machines with Mutagen
date: 2023-12-22 21:00
categories: [Guides, Software]
tags: [guide, development]
image: /assets/img/posts/2023-12-22-mutagen-intro/thumb.png

---

Mutagen is a tool that allows you to develop code on your local machine, and have it automatically synced to a remote machine. 

This is useful if you are developing code on a remote machine, but want to use your local machine to edit the code.

## Installation

The following steps are for Ubuntu, but you can find instructions for other operating systems on the [Mutagen website](https://mutagen.io/documentation/introduction/installation).


### Install homebrew

Homebrew is a package manager. It allows you to install software from the command line.

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Install mutagen

Using homebrew, mutagen can be installed with the following command.

```
brew install mutagen-io/mutagen/mutagen
```

## Create new session

To begin synchronization and forwarding, create new mutagen session.
```
mutagen sync create --name=web-app-code ~/project user@example.org:~/project
```
This command specifies a name for the session (`web-app-code`), the local directory to synchronize (`~/project`), and the remote directory to synchronize (`user@example.org:~/project`).

This command will configure the session and will ask for the remote machine's password. If you want to avoid entering the password each time you can use SSH keys to authenticate.

## Syncing files

Once the session has been created you should find that any file changes in the specified directories are automatically synced to the remote machine. No need to install anything on the remote machine or configure any additional access.

## List sessions

The `mutagen sync list` command can be used to list all sessions.

```
Name: modular-biped
Identifier: <identifier>
Alpha:
	URL: /home/dan/projects/modular-biped
	Connected: Yes
Beta:
	URL: pi@192.168.1.100:~/modular-biped
	Connected: Yes
Status: Scanning files

```

## Resuming sessions

When the device is disconnected, the session will be paused. To resume the session, use the `mutagen sync resume` command.

```
mutagen sync resume modular-biped
```