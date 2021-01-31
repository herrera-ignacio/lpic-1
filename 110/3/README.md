# 3. Securing data with encryption

Should be able to use public key techniques to secure data and communication.

## Key knowledge areas

* Perform basic OpenSSH 2 client configuration and usage.
* Understand the role of OpenSSH 2 server host keys.
* Perform basic GnuPG configuration, usage and revocation.
* Use GPG to encrypt, decrypt, sign and verify files.
* Understand SSH port tunnels (including X11 tunnels).

## Partial list of used files, terms and utilities

* ssh
* ssh-keygen
* ssh-agent
* ssh-add
* ~/.ssh/id_rsa and id_rsa.pub
* ~/.ssh/id_dsa and id_dsa.pub
* ~/.ssh/id_ecdsa and id_ecdsa.pub
* ~/.ssh/id_ed25519 and id_25519.pub
* /etc/ssh/ssh_host_rsa_key and /etc/ssh/ssh_host_rsa_key.pub
* /etc/ssh/ssh_host_dsa_key and ssh_host_dsa_key.pub
* /etc/ssh/ssh_host_ecdsa_key and ssh_host_ecdsa_key.pub
* /etc/ssh/ssh_host_ed25519_key and ssh_host_ed25519_key.pub
* ~/.ssh/authorized_keys
* ssh_known_hosts
* gpg
* gpg-agent
* ~/.gnupg/

## TOC

* Configure and enable SSH Service
  * OpenSSH tool suite
* SSH Service basics
  * Generating public-private key pair
  * Connecting to specific IP
  * `ssh-agent` and `ssh-add`
  * `ssh_known_hosts`
* Port forwarding via SSH (SSH Tunneling)
  * Local Forwarding
  * Remote Forwarding
  * Dynamic Forwarding
  * X11 Forwarding
* GnuPG (GPG)
  * Encrypt/decrypt files
  * Generating keys
  * Generating revocation certificate
  * Importing someone else's public key
  * Verifying and signing a key
  * How to share your public key
  * Refreshing your keys
  * `gpg-agent`

### Configure and enable SSH Service

SSH allows to access another computer through a secure and two-way encrypted communication system over the internet. Enabling SSH can connect your device to another device or server through a secure tunnel where your identity and data transmissions are totally safe and encrypted.

One user has a public key, and another user holds a private key. Data transmission can occur when both users enter the right encryption key.

#### OpenSSH

OpenSSH is the premier connectivity tool for remote login with the SSH protocol. It encrypts all traffic and provides a large suite of secure tunneling capabilities, several authentication methods, and configuration options.

It consists of the following tools:

* Remote operations are done using `ssh`, `scp`, and `sftp`.
* Key management with `ssh-add`, `ssh-keysign`, `ssh-keyscan`, and `ssh-keygen`.
* Service consists of `sshd`, `sftp-server` and `ssh-agent`.

#### Ubuntu

```
ssh -V
sudo apt update
sudo apt upgrade
sudo apt install openssh-server
```

#### Red Hat Linux and CentOS

```
dnf|yum install openssh-server
systemctl start sshd
systemctl enable sshd
firewall-cmd --zone=public --permanent --add-service=ssh
```

### SSH Service basics

#### Generating public-private key pair

```
ssh-keygen
```

The private key must remain hidden while the public key must be copied to the remote host to be used for establishing the SSH connection.

#### Connecting to specific IP

```
ssh username@ip_address
```

#### `ssh-agent` & `ssh-add`

`ssh-agent` is a background program that handles passwords for SSH private keys. The `ssh-add` command prompts the user for a private key password and adds it to the lsit maintained by `ssh-agent`. Once you add a password to it, you will not be prompted for it when using SSH or scp to connect to hosts with your public key.

The public part of the key loaded into the agen must be put on the target system in `~/.ssh/authorized_keys`.

#### `ssh_known_hosts`

The `/etc/ssh/ssh_known_hosts` and `~/.ssh/known_hosts` files contain the host public keys for all known hosts. The use of the global file is optional. The per-user file is maintained automatically. Each time the user connects from an unknown host, the key of that unknown host is added to the per-user file.

The known hosts file also provides a facility to mark keys as revoked, for example when it is known that the associated private key has been stolen. Revoked keys are specified by including the ``@revoked'' marker at the beginning of the key line, and are never accepted for authentication or as certification authorities, but instead will produce a warning from ssh when they are encountered.

### Port Forwarding via SSH (SSH Tunneling)

Port Forwarding via SSH (__SSH Tunneling__) creates a secure connection between a local computer and a remote machine through which services can be relayed. Because the connection is encrypted, SSH tunneling is useful for transmitting information that uses an unecrypted protocol, such as IMAP, VNC or IRC.

You need to make sure port forwarding is enabled in your server (look in `sshd_config` for `AllowTcpForwarding` and `X11Forwarding`), and tell your client the source and destination port numbers to use. If you're using local or remote forwarding, you need to tell your client the destination server. If you're using dynamic port forwarding, you need to configure your programs to use a SOCKS proxy server.

#### Local forwarding

Connections from the SSH client are forwarded via the SSH server, then to a destination server.

This is the most common type. For example, it lets you bypass a company firewall that blocks a page.

To use local port forwarding, you need to know your destination server and two port numbers. For example, say you wanted to connect to ubuntuforums.org using an SSH tunnel, you would use source port number 8080 (alternate http port), destination port 80, and destination server ubuntuforums.org:

```
ssh -L 8080:ubuntuforums.org:80 <host>
ssh -L 8080:www.ubuntuforums.org:80 -L 12345:ubuntu.com:80 <host>
```

Then pointint your browser to `localhost:8080` would download pages from ubuntuforums.org, and `localhost:12345` from ubuntu.com.

#### Remote forwarding

Connections from the SSH server are forwarded via the SSH client, then to a destination server. This lets you connect from the remote SSH server to another server.

For example, it lets you connect from your SSH server to a computer on your company's intranet.

To use remote forwarding you need to know your destination server, and two port numbers. For example, say you wanted to let a friend access your remote desktop, you would use port number 5900 (first VNC port) and destination server localhost:

```
ssh -R 5900:localhost:5900 guest@friend-pc
```

For the duration of the SSH session, your friend would be able to access your desktop by connecting a VNC client to port 5900 on his computer (if you had set up a shared desktop).

#### Dynamic forwarding

Connections from various programs are forwarded via the SSH client, then via the SSH server, and finally to several destination servers. It turns your SSH client into a SOCKS proxy server (protocol for programs to request any Internet connection through a proxy server).

For example, it lets you bypass a company firewall that blocks web access altogether. Altough this is very powerful, it takes a lot of work to set up, and it's usually easier to use local port forwarding for the specific sites you want to access.

Each program that uses the proxy server needs to be configured specifically, and reconfigured when you stop using the proxy server.

For example, say you wanted Firefox to connect to every web page through your SSH server. First you would use dynamic port forwarding with the default SOCKS port:

```
ssh -C -D 1080 <host>
# -D for dynamic port forwarding
# -C enables compression, which speeds the tunnel up when proxying mainly text-based information
```

Next you would tell Firefox to use your proxy (127.0.0.1:1080).

#### X11 Forwarding

X11 forwarding is a method of allowing a user to start graphical applications installed on a remote Linux system and forward that application windows to the local system. The remote system need not to have X server or graphical desktop environment. Hence configuring X11 forwarding using SSH enables the user to securely run graphical applications over SSH session.

Make sure you have installed `xauth` on your remote server system (`dnf install xorg-x11-xauth`), and edit `/etc/ssh/ssh_config` to set `X11Forwarding` to `yes`. Restart your ssh service `systemctl restart sshd`.

Now, from your client system, access the remove server via SSH with `ssh -X root@ip` and launch any GUI application such as `gedit`.

### GnuPG

GnuPG (also known as GPG) is a complete and free implementation of the OpenPGP standard (also known as PGP), which allows you to encrypt and sign your data and communications. It features a versatile key management system, along with access modules for all kinds of public key directories.

GPG is a command line took with features for easy integration with other applications, and also provides support for S/MIME and SSH.

GPG relies on the idea of two encryption keys per person. Each person has a private key and a public key.

The first time you run any `gpg` command, a configuration directoring and keyring will be created in your home directory:

```
$ gpg --list-keys
gpg: directory '/home/username/.gnupg' created
gpg: keybox '/home/username/.gnupg/pubring.kbx' created
gpg: /home/username/.gnupg/trustdb.gpg: trustdb created
```

#### Encrypt/decrypt files

To send a file securely, you encrypt it with your private key and the recipient's public key. To decrypt the file, they need their private key and your public key (so public keys must be shared).

```
gpg --encrypt --sign --armor -r <receiver_email> <file>

# For decrypting, we don't have to tell gpg who the file is form,
# it can work that out from the encrypted contents of the file.
gpg --decrypt coded.asc > plain.txt
```

#### Generating keys

```
gpg --full-generate-key
```

#### Generating revocation certificate

If your private key becomes known to others, you will need to disassociate the old keys from your identity, so that you can generate new ones. To do this, you will require a revocation certificate. We'll do this now and store it somewhere safe.

```
# you need to provide the email used when keys were generated
gpg --output ~/revocation.crt --gen-revoke <email_address>

# remove all permissions apart from ours from the certificate
chmod 600 ~/revocation.crt
```

#### Importing someone else's public key

```
gpg --import <file>.key
gpg --keyserver pgp.mit.edu --search-keys <email>
```

#### Verifying and signing a key

You can ask the person to send you the fingerprint of their key and compare it:

```
gpg --fingerprint <email>
```

When you're satisfied that the key is genuine, you can sign their key. Otherwise, `gpg` will ask you every time whether you wish to proceed because the key is unsigned.

```
gpg --sign-key <email>
```

#### Share your public key

```
gpg --output ~/dave-geek.key --armor --export <your_email>
gpg --send-keys --keyserver pgp.mit.edu <fingerprint>
```

#### Refreshing your keys

Periodically, you can ask `gpg` to check the keys it has againast a public key server and to refresh any that have changed.

```
gpg --keyserver pgp.mit.edu --refresh-keys
```

#### `gpg-agent`

`gpg-agent` is a daemon to manage secret (private) keys independently from any protocol. It is used as a backend for `gpg` and `gpgsm` as well as for a couple of other utilities.
