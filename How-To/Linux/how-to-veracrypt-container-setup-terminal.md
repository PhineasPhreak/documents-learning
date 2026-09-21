# Veracrypt terminal-based container setup

A comprehensive terminal-based tutorial for setting up a VeraCrypt container from start to finish.

# Installation

First, install VeraCrypt on your system. The easiest method on Ubuntu/Debian-based systems is via PPA:

```bash
sudo add-apt-repository ppa:unit193/encryption
sudo apt update
sudo apt install veracrypt
```

For servers or headless systems, install the console-only version (no GUI dependencies):

```bash
sudo apt install veracrypt-console
```

Verify installation:

```bash
veracrypt --version
```

## Step 1: Create an Encrypted Container File

The simplest approach is creating a file-based container. This creates an encrypted file that acts like a disk image. Choose interactive mode for guided setup:

```bash
veracrypt --text --create
```

You'll be prompted for:
- **Container location and name** (e.g., `/home/user/secure-data.vc`)
- **Volume type**: Normal (standard) or Hidden (for plausible deniability)
- **Container size** (e.g., 1G, 500M)
- **Encryption algorithm**: AES (recommended for speed and hardware acceleration)
- **Hash algorithm**: SHA-512 (secure default)
- **Filesystem**: Choose based on your needs:
  - `ext4` for Linux-only use
  - `exFAT` or `FAT` for cross-platform compatibility
- **Password**: Enter a strong passphrase (20+ characters recommended)

## Step 2: Non-Interactive Container Creation (Scripting)

For automated setup without prompts, use this non-interactive approach:

```bash
veracrypt -t --non-interactive --create /home/user/data.vc \
  --size 1G \
  --encryption AES \
  --hash sha-512 \
  --filesystem exFAT \
  --password "YourStrongPassphrase123!" \
  --pim=0 \
  --keyfiles="" \
  --volume-type normal \
  --random-source /dev/urandom
```

**Parameters explained:**
- `--size`: Container size (1G, 500M, etc.)
- `--encryption`: Algorithm (AES, Serpent, Twofish, or cascaded combinations)
- `--hash`: Key derivation hash (SHA-512, SHA-256, etc.)
- `--filesystem`: exFAT for cross-platform, ext4 for Linux-only
- `--pim`: PIM (Personal Iteration Multiplier) for password iteration count; 0 uses default
- `--random-source`: Source for random data during creation

**Security note:** Avoid putting passwords in scripts. Use stdin redirection instead (see below).

## Step 3: Create Mount Point

Before mounting, create a directory where the encrypted volume will be accessible:

```bash
sudo mkdir -p /mnt/secure
```

## Step 4: Mount the Container

Mount interactively (VeraCrypt prompts for password):

```bash
sudo veracrypt --text --mount /home/user/data.vc /mnt/secure
```

You'll be prompted for the password. After successful authentication, the decrypted volume is accessible at `/mnt/secure`.

## Step 5: Mount with Password from File (Non-Interactive)

For automation, store the password in a restricted file and use stdin:

```bash
# Create a password file with restricted permissions
sudo sh -c 'printf "%s\n" "YourStrongPassphrase123!" > /root/.vc-password'
sudo chmod 400 /root/.vc-password
```

Then mount using the password file:

```bash
sudo sh -c 'veracrypt -t --non-interactive --stdin --pim=0 --keyfiles="" \
  --protect-hidden=no /home/user/data.vc /mnt/secure < /root/.vc-password'
```

## Step 6: Verify the Mount

Check that your volume mounted successfully:

```bash
veracrypt --text --list
```

Output example:
```
Slot 1: /home/user/data.vc mounted at /mnt/secure
```

You can also use standard tools:

```bash
df -h | grep secure
mount | grep secure
```

## Step 7: Use Your Encrypted Volume

Now you can read, write, and manage files normally:

```bash
# Copy files into the container
cp /path/to/sensitive/file /mnt/secure/

# Create directories
mkdir /mnt/secure/projects

# List contents
ls -la /mnt/secure/
```

## Step 8: Unmount the Container

Always unmount properly before removing media or shutting down:

```bash
# Unmount by mount point
sudo veracrypt --text --unmount /mnt/secure

# Or unmount by slot number
sudo veracrypt --text --unmount --slot=1

# Unmount all VeraCrypt volumes
sudo veracrypt --text --unmount
```

## Troubleshooting

**"fuse: device not found"** https://oneuptime.com/blog/post/2026-03-02-how-to-configure-veracrypt-for-cross-platform-encrypted-volumes-on-ubuntu/view

Install FUSE support:

```bash
sudo apt install fuse3
sudo modprobe fuse
```

**"Volume contains wrong signature"**

This means the password was incorrect or the file isn't a valid VeraCrypt container. Double-check your password and file path.

**Slow container creation**

For large containers, use quick formatting (note: don't use this for outer volumes with hidden volumes):

```bash
veracrypt -t --non-interactive --create /home/user/data.vc \
  --quick \
  --size 5G \
  ... [other parameters]
```

**Read-only volume**

Mount with explicit write permissions:

```bash
sudo veracrypt --text --mount /home/user/data.vc /mnt/secure --fs-options=rw
```

## Key Takeaways

- **File containers** are portable and easy to back up or move between systems https://oneuptime.com/blog/post/2026-03-02-how-to-configure-veracrypt-for-cross-platform-encrypted-volumes-on-ubuntu/view
- **exFAT filesystem** provides cross-platform compatibility (Linux, Windows, macOS) https://oneuptime.com/blog/post/2026-03-02-how-to-configure-veracrypt-for-cross-platform-encrypted-volumes-on-ubuntu/view
- **AES encryption** is the best choice for performance and hardware acceleration support https://oneuptime.com/blog/post/2026-03-02-how-to-configure-veracrypt-for-cross-platform-encrypted-volumes-on-ubuntu/view
- **Always use strong passwords** (20+ characters) and store them securely
- **Unmount before powering off** to prevent data corruption

This workflow covers everything from creation to daily use entirely from the terminal.
