# Steps to rename a user on Ubuntu Server
- **Log out of the users account you intent to rename.**

- Log in as another user with `sudo` privileges or as the `root` user. If you don't have another `sudo` user, you might need to create a temporary one or log in as root (if enabled) via a virtual console (e.g., Ctrl+Alt+F1).

- **Rename the user account:**
  ```shell
  sudo usermod -l new_username old_username
  ```
> Replace `new_username` with your desired new username and `old_username` with the current username you are changing.

- **Rename the primary group associated with the user:**
  ```shell
  sudo groupmod -n new_username old_username
  ```
> This command changes the name of the group that matches the old username to the new username.

- **Update and move the user's home directory:**
  ```shell
  sudo usermod -d /home/new_username -m new_username
  ```
> The `-d` option specifies the new home directory path, and the `-m` option ensures the contents of the old home directory are moved to the new location.

- **Verify the changes:** You can check the `/etc/passwd` file to confirm the username and home directory have been updated:
  ```shell
  grep new_username /etc/passwd
  ```
- **Log out of the administrative user and log in with the new username.**
