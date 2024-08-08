# How to set up Python3 for Emacs

# Distribution packages
## Packages to install
Depending on your distribution (Debian, Ubuntu, Arch Linux), install the various `python3-*` packages.

For **Debian**, **Ubuntu** :

```shell
sudo apt install python3-pylsp python3-pylsp-isort python3-pylsp-black python3-pip python3-venv
```

For **Archlinux** :

```shell
sudo pacman -Sy python-lsp-server python-lsp-black python-lsp-jsonrpc
```

## Optional packages
Additional packages that can be installed.

For **Debian**, **Ubuntu** :

```shell
sudo apt install python3-autopep8
```

# Emacs packages
Install [packages in emacs](https://github.com/PhineasPhreak/dotfiles/blob/master/configs/emacs/README.md) :

Packages : `atom-one-dark-theme-0.4.0`, `csv-mode`, `markdown-mode`, `auctex`, `shell-pop`, `company`, `flycheck`, `eglot`

```
M-x package-install RET <Packages_Name>
```

# Configure `Custom.el`
See, [Emacs folder](https://github.com/PhineasPhreak/dotfiles/tree/master/configs/emacs)
