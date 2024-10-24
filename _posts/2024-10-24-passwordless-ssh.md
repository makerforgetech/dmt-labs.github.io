---
layout: post
title: Using SSH without passwords
date: 2024-10-23 19:24
categories: [Guides, Software]
tags: [guide, development]
---

I love being able to remote into my Raspberry Pi, but sometimes entering the username / password combo can be time consuming, especially after multiple restarts. Let's take a look at a way to store your credentials so that the connection can be made easily.

## Setting Up SSH Passwordless Login: A Step-by-Step Guide for Makers

Secure Shell (SSH) is a powerful tool that allows users to perform a wide range of tasks remotely, from accessing servers to automating backups and synchronizing files. One of the most efficient and secure ways to manage these tasks is by setting up SSH passwordless login, which uses a pair of cryptographic keys to authenticate users without needing to enter a password each time.

In this guide, we’ll walk you through the process of setting up SSH passwordless login on a Linux server. By the end, you’ll be able to connect securely without repeatedly typing your password, while also maintaining security by using a public and private key pair.

### Why Use SSH Passwordless Login?

SSH passwordless login improves security and convenience, but it can come with challenges:
- **Rotating credentials**: When team members come and go, new credentials (either passwords or keys) must be created, and the old ones removed.
- **Auditing access**: While SSH is secure, wrapping communication in an SSH tunnel can make tracking and managing access more difficult.
- **Credential rotation**: Keeping keys updated can be a time-consuming and overlooked task.

Luckily, automating these processes can make managing SSH keys much easier, as we'll cover later.

### Setting Up SSH Passwordless Login

Let’s get started on configuring SSH passwordless login. The following steps apply to most modern Linux distributions and include specific instructions for macOS and Windows clients as well.

#### Step 1: Generate a Key Pair

To establish SSH passwordless login, you first need to generate a public and private key pair on your client machine.

In a terminal (Linux/macOS) or Command Prompt (Windows), enter:

```bash
ssh-keygen -t rsa
```

This command tells the system to generate an RSA key. While RSA is the most common, you can also choose from other algorithms like DSA, ECDSA, or ED25519 depending on your setup.

You’ll be prompted to specify a file name for the keys:

```bash
Enter file in which to save the key (C:\Users\your_username\.ssh\id_rsa):
```

It’s fine to press `Enter` here and use the default path. If this is your first key or primary key, it’s recommended to stick with the default `id_rsa` naming convention.

Next, you’ll be asked to create a passphrase. This step is essential to securing your private key—while optional, it’s highly recommended to add a passphrase to protect the key if it’s ever compromised.

```bash
Enter passphrase (empty for no passphrase):
```

Choose a secure passphrase, press `Enter`, and confirm it when prompted. You’ll now have two files:
- `id_rsa`: Your private key.
- `id_rsa.pub`: Your public key.

#### Step 2: Set Up the SSH Directory on the Server

Now, it’s time to set up the server to accept your public key. First, log into your server using SSH with your existing username and password. Once connected, check if the `.ssh` directory exists:

```bash
ls .ssh
```

If the directory doesn’t exist, create it:

```bash
mkdir -p .ssh
```

The `-p` option ensures that the directory is created along with its parent directories if they don’t already exist.

#### Step 3: Upload the Public Key to the Server

Next, you’ll upload your public key to the server.

You’ll need to manually upload the key using the `scp` command. If your server doesn’t already have an `authorized_keys` file, use this command:

```bash
scp .ssh/id_rsa.pub user@yourserver.com:~/.ssh/authorized_keys
```

If there is an existing `authorized_keys` file, append your key to avoid removing existing access:

1. Copy the key to the server:
   ```bash
   scp .ssh/id_rsa.pub user@yourserver.com:~/.ssh
   ```

2. Log into the server and append the key:
   ```bash
   cat .ssh/id_rsa.pub >> .ssh/authorized_keys
   ```

3. Clean up the server:
   ```bash
   rm .ssh/id_rsa.pub
   ```

#### Step 4: Adjust Permissions and Test the Connection

Once the key is uploaded, ensure the correct file permissions are set. On the server, enter the following commands:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

These commands ensure that only the owner can read or write to the `.ssh` directory and the `authorized_keys` file.

Now, disconnect from the server and try reconnecting:

```bash
ssh user@yourserver.com
```

### Wrapping Up

With SSH passwordless login configured, you’ve now streamlined your workflow for accessing servers securely and conveniently. Remember to regularly rotate your keys, back them up, and always use a passphrase to keep your private key secure.

For even more advanced setups, you can integrate SSH with automated tools and agents that further simplify access management and security auditing, taking your system to the next level. Happy making!
