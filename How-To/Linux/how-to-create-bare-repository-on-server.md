# Git how to create a bare repository on local or remote server
When you use Git, the workflow generally is toward version control only. You have a local repository where you work and a remote repository where you keep everything in sync and can work with a team and different machines.

Link to GitPro and Video :
- Chapters 4.1 [Git on the Server - The Protocols](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)
- Video de Tsoding [Video Tsoding](https://www.youtube.com/watch?v=iuIdBfjL62s)


# Creating Our Repository (Server Machine)
# Server Setup
## Bare repositories
Create an empty directory, which will later become the git folder.
```shell
# Creating a local directory for git repository.
mkdir ~/Gitlocal/
```

Got to this directory `~/Gitlocal`

```shell
git init --bare myrepo
```

Initialize an empty Git repository, but omit the working directory. Shared repositories should always be created with the `--bare` flag (see discussion below). Conventionally, repositories initialized with the `--bare` flag end in `.git`. For example, the bare version of a repository called `my-project` should be stored in a directory called `my-project.git`.

The `--bare` flag creates a repository that doesn’t have a working directory, making it impossible to edit files and commit changes in that repository. You would create a bare repository to git push and git pull from, but never directly commit to it. Central repositories should always be created as bare repositories because pushing branches to a non-bare repository has the potential to overwrite changes. Think of `--bare` as a way to mark a repository as a storage facility, as opposed to a development environment. This means that for virtually all Git workflows, the central repository is bare, and developers local repositories are non-bare.

> What just happened:
> - Git created a directory called `myproject.git`
> - This directory **is the repository**
> - There is **no working directory**
> - You will NOT edit files here

First, you SSH into the server that will contain your central repository. Then, you navigate to wherever you’d like to store the project. Finally, you use the `--bare` flag to create a central storage repository. Developers would then clone `my-project.git` to create a local copy on their development machine.


# Converting a Local Repository to Bare
If you already have a local repository, you can clone it as bare :
```shell
git clone --bare Gitlocal/path_to_local_repo
```


# How to clone your repositories from the server
## Local Machine
Using git with ssh information.
```shell
git clone <user>@<host>:Gitlocal/myrepo
```

Or, using this command if you using custom port for the ssh connection.
```shell
git clone ssh://<user>@<host>:[port]/full/to/path/Gitlocal/myrepo
```

Other solution, Use the `config` file from the [ssh configuration](https://www.ssh.com/academy/ssh) under `.ssh/`.
```shell
git clone rift-vps-srv:Gitlocal/myrepo
```


## Connection with Emacs using ssh
Open a new file `CTRL+x f` and type the following command :
```shell
/ssh:<user>@<host>#2222:Gitlocal/myrepo
```
