# How To Set Up vsftpd for a User's Directory on Ubuntu 18.04

Link : [Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-vsftpd-for-a-user-s-directory-on-ubuntu-18-04)

## Introduction

`FTP`, short for File Transfer Protocol, is a network protocol that was once widely used for moving files between a client and server. It has since been replaced by faster, more secure, and more convenient ways of delivering files. Many casual Internet users expect to download directly from their web browser with `https`, and command-line users are more likely to use secure protocols such as the `scp` or [SFTP](https://www.digitalocean.com/community/tutorials/how-to-use-sftp-to-securely-transfer-files-with-a-remote-server).

FTP is still used to support legacy applications and workflows with very specific needs. If you have a choice of what protocol to use, consider exploring the more modern options. When you do need FTP, however, vsftpd is an excellent choice. Optimized for security, performance, and stability, vsftpd offers strong protection against many security problems found in other FTP servers and is the default for many Linux distributions.

In this tutorial, you’ll configure vsftpd to allow a user to upload files to his or her home directory using FTP with login credentials secured by SSL/TLS.

## Prerequisites

To follow along with this tutorial you will need :

* **An Ubuntu 18.04 server, and a non-root user with sudo privileges:** 
  You can learn more about how to set up a user with these privileges in our [Initial Server Setup with Ubuntu 18.04 guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04).

## Step 1 — Installing vsftpd

Let’s start by updating our package list and installing the `vsftpd` daemon :

```console
$ sudo apt update
$ sudo apt install vsftpd
```
When the installation is complete, let’s copy the configuration file so we can start with a blank configuration, saving the original as a backup :

```console
$ sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.orig
```
With a backup of the configuration in place, we’re ready to configure the firewall.

## Step 2 — Opening the Firewall *(With UFW)*

Let’s check the firewall status to see if it’s enabled. If it is, we’ll ensure that FTP traffic is permitted so firewall rules don’t block our tests.

Check the firewall status:

```console
$ sudo ufw status
```

In this case, only SSH is allowed through:

```console
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
```

You may have other rules in place or no firewall rules at all. Since only SSH traffic is permitted in this case, we’ll need to add rules for FTP traffic.

Let’s open ports `20` and `21` for FTP, port `990` for when we enable TLS, and ports `40000-50000` for the range of passive ports we plan to set in the configuration file:

```console
$ sudo ufw allow 20/tcp
$ sudo ufw allow 21/tcp
$ sudo ufw allow 990/tcp
$ sudo ufw allow 40000:50000/tcp
$ sudo ufw status
```

Our firewall rules should now look like this:

```console
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
990/tcp                    ALLOW       Anywhere
20/tcp                     ALLOW       Anywhere
21/tcp                     ALLOW       Anywhere
40000:50000/tcp            ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
20/tcp (v6)                ALLOW       Anywhere (v6)
21/tcp (v6)                ALLOW       Anywhere (v6)
990/tcp (v6)               ALLOW       Anywhere (v6)
40000:50000/tcp (v6)       ALLOW       Anywhere (v6)
```

With `vsftpd` installed and the necessary ports open, let’s move on to creating a dedicated FTP user.

## Step 3 — Preparing the User Directory

We will create a dedicated FTP user, but you may already have a user in need of FTP access. We’ll take care to preserve an existing user’s access to their data in the instructions that follow. Even so, we recommend that you start with a new user until you’ve configured and tested your setup.

First, add a test user:

```console
$ sudo adduser sammy
```

Assign a password when prompted. Feel free to press `ENTER` through the other prompts.

FTP is generally more secure when users are restricted to a specific directory. vsftpd accomplishes this with `chroot` jails. When `chroot` is enabled for local users, they are restricted to their home directory by default. However, because of the way `vsftpd` secures the directory, it must not be writable by the user. This is fine for a new user who should only connect via FTP, but an existing user may need to write to their home folder if they also have shell access.

In this example, rather than removing write privileges from the home directory, let’s create an ftp directory to serve as the `chroot` and a writable `files` directory to hold the actual files.

Create the `ftp` folder:
```console
$ sudo mkdir /home/sammy/ftp
```

Set its ownership:
```console
$ sudo chown nobody:nogroup /home/sammy/ftp
```

Remove write permissions:
```console
$ sudo chmod a-w /home/sammy/ftp
```

Verify the permissions:
```console
$ sudo ls -la /home/sammy/ftp
```

```console
Output
total 8
4 dr-xr-xr-x  2 nobody nogroup 4096 Aug 24 21:29 .
4 drwxr-xr-x  3 sammy  sammy   4096 Aug 24 21:29 ..
```

Next, let’s create the directory for file uploads and assign ownership to the user:
```console
$ sudo mkdir /home/sammy/ftp/files
$ sudo chown sammy:sammy /home/sammy/ftp/files
```

A permissions check on the `ftp` directory should return the following:

```console
$ sudo ls -la /home/sammy/ftp
```
```console
Output
total 12
dr-xr-xr-x 3 nobody nogroup 4096 Aug 26 14:01 .
drwxr-xr-x 3 sammy  sammy   4096 Aug 26 13:59 ..
drwxr-xr-x 2 sammy  sammy   4096 Aug 26 14:01 files
```

Finally, let’s add a `test.txt` file to use when we test:

```console
$ echo "vsftpd test file" | sudo tee /home/sammy/ftp/files/test.txt
```

Now that we’ve secured the `ftp` directory and allowed the user access to the `files` directory, let’s modify our configuration.

## Step 4 — Configuring FTP Access

We’re planning to allow a single user with a local shell account to connect with FTP. The two key settings for this are already set in `vsftpd.conf`. Start by opening the config file to verify that the settings in your configuration match those below:

```console
$ sudo nano /etc/vsftpd.conf
```

> **/etc/vsftpd.conf**
> ```properties
> . . .
> # Allow anonymous FTP? (Disabled by default).
> anonymous_enable=NO
> #
> # Uncomment this to allow local users to log in.
> local_enable=YES
> . . .
> ```

Next, let’s enable the user to upload files by uncommenting the `write_enable` setting:

> **/etc/vsftpd.conf**
> ```properties
> . . .
> write_enable=YES
> . . .
> ```

We’ll also uncomment the `chroot` to prevent the FTP-connected user from accessing any files or commands outside the directory tree:

> **/etc/vsftpd.conf**
> ```properties
> . . .
> chroot_local_user=YES
> . . .
> ```

Let’s also add a `user_sub_token` to insert the username in our `local_root` directory path so our configuration will work for this user and any additional future users. Add these settings anywhere in the file:

> **/etc/vsftpd.conf**
> ```properties
> . . .
> user_sub_token=$USER
> local_root=/home/$USER/ftp
> ```

Let’s also limit the range of ports that can be used for passive FTP to make sure enough connections are available:

> **/etc/vsftpd.conf**
> ```properties
> . . .
> pasv_min_port=40000
> pasv_max_port=50000
> ```

> **Note:** 
> 
> In step 2, we opened the ports that we set here for the passive port range. If you change the values, be sure to update your firewall settings.

To allow FTP access on a case-by-case basis, let’s set the configuration so that users have access only when they are explicitly added to a list, rather than by default:

> **/etc/vsftpd.conf**
> ```properties
> . . .
> userlist_enable=YES
> userlist_file=/etc/vsftpd.userlist
> userlist_deny=NO
> ```

`userlist_deny` toggles the logic: When it is set to `YES`, users on the list are denied FTP access. When it is set to `NO`, only users on the list are allowed access.

When you’re done making the changes, save the file and exit the editor.

Finally, let’s add our user to `/etc/vsftpd.userlist`. Use the `-a` flag to append to the file:

```console
$ echo "sammy" | sudo tee -a /etc/vsftpd.userlist
```

Check that it was added as you expected:

```console
$ cat /etc/vsftpd.userlist
```
```console
Output
sammy
```

Restart the daemon to load the configuration changes:

```console
$ sudo systemctl restart vsftpd
```

With the configuration in place, let’s move on to testing FTP access.

## Step 5 — Testing FTP Access

