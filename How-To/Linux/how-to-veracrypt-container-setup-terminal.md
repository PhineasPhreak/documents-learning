VeraCrypt Command Line Tutorial for Linux

---

## Chapter 1 - Prerequisites

Key steps:
1. Use a machine with at least about 50 GB of free disk space (45 GB container plus filesystem and header overhead).
2. Confirm you have sudo rights.
3. Install FUSE support, required for file containers.
4. Prefer a strong passphrase of 20+ characters: long, unique, mixed character classes, not reused elsewhere.
5. Do not use `--quick` if you want the strongest creation process. Quick format skips filling the container with random data.

Ubuntu FUSE:

```bash
sudo apt update
sudo apt install -y fuse3
```

Fedora FUSE:

```bash
sudo dnf install -y fuse fuse-libs
```

---

## Chapter 2 - Install VeraCrypt on Ubuntu (console)

Prefer the official console package from the VeraCrypt downloads page so you do not need the GUI. Current packaged line is 1.26.29; replace the Ubuntu codename and filename with the exact files listed for your release.

Key steps:
1. Identify your Ubuntu version:

```bash
lsb_release -a
uname -m
```

2. Download the matching console `.deb` from https://www.veracrypt.fr/en/Downloads.html (example for Ubuntu 24.04 amd64):

```bash
cd ~/Downloads
# Replace the URL/filename with the exact console package for your Ubuntu version
wget https://launchpad.net/veracrypt/trunk/1.26.29/+download/veracrypt-console-1.26.29-Ubuntu-24.04-amd64.deb
```

3. Verify the SHA256 published next to the file on the official site before installing.
4. Install:

```bash
sudo apt install -y ./veracrypt-console-1.26.29-Ubuntu-24.04-amd64.deb
```

Alternative via community PPA (updates through APT, not the official site):

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:unit193/encryption
sudo apt update
sudo apt install -y veracrypt
```

---

## Chapter 3 - Install VeraCrypt on Fedora (console)

Key steps:
1. Check release and architecture:

```bash
cat /etc/fedora-release
uname -m
```

2. Download the matching console `.rpm` from the official Downloads page (example names: `veracrypt-console-1.26.29-Fedora-43-x86_64.rpm` or Fedora 44).
3. Verify the published SHA256.
4. Install:

```bash
sudo dnf install -y ./veracrypt-console-1.26.29-Fedora-43-x86_64.rpm
```

If a dependency is missing, install FUSE first, then retry the rpm/dnf command.

---

## Chapter 4 - Verify the CLI

Key steps:
1. Confirm the binary:

```bash
veracrypt --version
which veracrypt
```

2. Open text-mode help. `--text` / `-t` must be the first argument:

```bash
veracrypt -t --help
```

3. Self-test algorithms:

```bash
veracrypt -t --test
```

---

## Chapter 5 - Strongest practical crypto settings

“Most robust” in VeraCrypt means a triple cascade plus a strong KDF hash, no quick format, a long password, and optionally a higher PIM. AES-256 alone is already considered unbroken in practice; a cascade adds defense if one cipher is later broken, at the cost of speed.

Recommended profile for this tutorial:
- Volume type: `normal` (file container)
- Encryption: `AES-Twofish-Serpent` (three independent 256-bit ciphers in XTS)
- Hash / PRF: `SHA-512`
- PIM: default (`0`) is already high. For extra brute-force cost use a custom PIM such as `1000` (mount will be slower). Formula for non-system volumes: iterations = 15000 + (PIM * 1000).
- Filesystem: `ext4` on Linux
- Keyfiles: optional extra factor; omit unless you will back them up as carefully as the password
- Random source: `/dev/urandom` (practical). `/dev/random` may block for a 45 GB creation.
- Password: 20 characters minimum, entered interactively. Avoid `-p` / `--password` on the command line: it can appear in `ps` and shell history.

Do not enable `--quick`.

---

## Chapter 6 - Create a 45 GB container

Choose a path on a filesystem with enough free space, for example `$HOME/secure/vault.hc`.

Key steps:
1. Create the directory and check free space:

```bash
mkdir -p "$HOME/secure"
df -h "$HOME"
```

2. Preferred method: interactive text wizard (password is typed, not stored in history):

```bash
sudo veracrypt -t -c
```

Answers to give when prompted:
- Volume type: `1` (normal volume) or type `normal` depending on the prompt
- Volume location: `/home/YOUR_USER/secure/vault.hc` (use a new file, never an existing file you want to keep)
- Encryption algorithm: `AES-Twofish-Serpent`
- Hash algorithm: `SHA-512`
- Size: `45G`
- Password: at least 20 characters, then confirm
- PIM: press Enter for default, or enter e.g. `1000`
- Keyfiles: none (empty)
- Filesystem: `ext4`
- Cross-platform support: no (Linux ext4)
- Random data: move the mouse / type random keys if asked, or rely on `--random-source` in the non-interactive form below

3. Non-interactive equivalent (still better to omit `--password` and let VeraCrypt prompt). Creation of 45 GB without `--quick` takes a long time:

```bash
sudo veracrypt -t --create "$HOME/secure/vault.hc" \
  --volume-type=normal \
  --size=45G \
  --encryption=AES-Twofish-Serpent \
  --hash=SHA-512 \
  --filesystem=ext4 \
  --pim=0 \
  --keyfiles="" \
  --random-source=/dev/urandom
```

Then enter the password twice when asked.

4. Confirm the file exists and is about 45 GB:

```bash
ls -lh "$HOME/secure/vault.hc"
```

If you set a custom PIM, write it down with the password. A wrong PIM looks like a wrong password.

---

## Chapter 7 - Mount (open) the container

Mounting decrypts on the fly and attaches a filesystem at a mount point. That is how you “open” the container in the terminal.

Key steps:
1. Create a mount point:

```bash
sudo mkdir -p /mnt/veracrypt-vault
```

2. Mount in text mode (recommended: no password on the command line):

```bash
sudo veracrypt -t --keyfiles="" --protect-hidden=no \
  "$HOME/secure/vault.hc" /mnt/veracrypt-vault
```

Enter:
- Password
- PIM (Enter if you used default / `0`)
- Keyfile: empty
- Protect hidden volume: `n`

3. If the volume was formatted as ext4 by root, fix ownership once after the first mount:

```bash
sudo chown -R "$USER:$USER" /mnt/veracrypt-vault
```

4. Confirm it is mounted:

```bash
veracrypt -t --list
df -h /mnt/veracrypt-vault
ls -la /mnt/veracrypt-vault
```

5. Optional: mount read-only:

```bash
sudo veracrypt -t -m ro --keyfiles="" --protect-hidden=no \
  "$HOME/secure/vault.hc" /mnt/veracrypt-vault
```

Do not copy the container file while it is mounted. Work only through the mount point.

---

## Chapter 8 - Use the opened volume

Key steps:
1. Read and write like any directory:

```bash
cp /path/to/file /mnt/veracrypt-vault/
ls -lh /mnt/veracrypt-vault
```

2. Sync before unmounting after large writes:

```bash
sync
```

3. Check properties of the mounted volume:

```bash
sudo veracrypt -t --volume-properties /mnt/veracrypt-vault
```

The files on disk remain encrypted. Only the mounted view is plaintext.

---

## Chapter 9 - Unmount (close) correctly

Always unmount before shutdown, suspend, or copying the `.hc` file. `--unmount` is the current option; `--dismount` still works but is deprecated.

Key steps:
1. Leave the mount point (if your shell is inside it):

```bash
cd ~
```

2. Unmount by mount point:

```bash
sudo veracrypt -t --unmount /mnt/veracrypt-vault
```

3. Or unmount by container path:

```bash
sudo veracrypt -t --unmount "$HOME/secure/vault.hc"
```

4. Or unmount every VeraCrypt volume:

```bash
sudo veracrypt -t --unmount
```

5. If a process still uses the volume:

```bash
sudo lsof +f -- /mnt/veracrypt-vault
sudo veracrypt -t --unmount --force /mnt/veracrypt-vault
```

Use `--force` only after you understand why the volume is busy.

6. Verify nothing remains mounted:

```bash
veracrypt -t --list
mount | grep -i veracrypt || true
```

After a clean unmount, `/mnt/veracrypt-vault` is empty and `vault.hc` is sealed again.

---

## Chapter 10 - Daily commands and hygiene

List volumes:

```bash
veracrypt -t --list
veracrypt -t --list --verbose
```

Backup the volume header (store the backup separately from the container):

```bash
veracrypt -t --backup-headers "$HOME/secure/vault.hc"
```

Good practice:
1. Never put the real password in scripts, aliases, or shell history. If it leaked into history: `history -d` the line and consider changing the volume password with `veracrypt -t --change`.
2. Keep at least one offline copy of `vault.hc` created only while the volume is unmounted.
3. A 20-character password is a minimum, not an optimum. Longer random passphrases are better.
4. Triple cascade plus SHA-512 is conservative; AES + SHA-512 is already strong and much faster if the cascade is too slow on your CPU.
5. Do not store the only copy of the password in the same place as the container.
