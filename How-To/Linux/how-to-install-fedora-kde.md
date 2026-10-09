# Fedora 44 KDE Plasma - Installation

Other, Full Setup Guide - [github.com/26zl](https://github.com/26zl/fedora-kde-setup#fedora-44-kde--full-setup-guide)

Fedora 44 [quick-docs](https://docs.fedoraproject.org/en-US/quick-docs/) on [RPM Fusion NVIDIA drivers](https://docs.fedoraproject.org/en-US/quick-docs/rpmfusion-setup/) and the package groups so the guide matches the recommended steps.

This guide targets a standard Fedora 44 KDE Plasma installation (DNF, not Atomic/Kinoite). It follows Fedora Quick Docs for RPM Fusion and multimedia, and the [RPM Fusion NVIDIA](https://docs.fedoraproject.org/km/gaming/drivers/) guide that those docs point to.

Proprietary NVIDIA drivers and some codecs are third-party. **Keep Secure Boot in mind before installing the driver**, or the module will not load and you can get a blank screen.

## 1. Update the system

Open Konsole and fully update, then reboot so you are on the latest kernel before building the NVIDIA module:

```bash
sudo dnf upgrade --refresh
sudo reboot
```

After reboot, confirm the running kernel:

```bash
uname -r
```

## 2. Enable RPM Fusion

Fedora documents third-party **free** and **nonfree** repositories on the [RPM Fusion setup Quick Doc](https://docs.fedoraproject.org/en-US/quick-docs/rpmfusion-setup/).
**NVIDIA lives in nonfree**; full multimedia codecs need both.

### Procedure:

1. To enable the `Free` repository, use:
   ```bash
   sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
   ```

2. Optionally, enable the `Nonfree` repository:
   ```bash
   sudo dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
   ```

Mirror, link (only if necessary):
```bash
sudo dnf install \
https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

The first time you attempt to install packages from these repositories, the `dnf` utility prompts you to confirm the signature of the repositories.

Accept the RPM Fusion GPG keys when prompted. Refresh metadata and confirm the repos:

```bash
sudo dnf upgrade --refresh
dnf repolist | grep -i rpmfusion
```

You should see `rpmfusion-free` and `rpmfusion-nonfree` (and their updates repos).

## 3. Indispensable workstation tools

These are not a single Fedora group, but they cover firmware, networking, archives, and day-to-day KDE use:

```bash
sudo dnf install \
  kernel-devel kernel-headers gcc make dkms \
  git curl wget rsync unzip p7zip p7zip-plugins \
  nano micro bash-completion htop btop fastfetch \
  fwupd
```

`kernel-devel` must match the running kernel. If a later NVIDIA build fails, install the matched headers explicitly:

```bash
sudo dnf install "kernel-devel-uname-r == $(uname -r)"
```

Enable Flathub (Fedora KDE already ships Flatpak; this adds the main remote):

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Firmware updates, if the vendor publishes them:

```bash
sudo fwupdmgr refresh
sudo fwupdmgr get-updates
sudo fwupdmgr update
```

## 4. Development Tools group

Fedora’s `Development Tools` group installs the C/C++ toolchain (gcc, g++, make, binutils, autotools, rpm build helpers, and related defaults).
Inspect it first if you want the package list:

```bash
dnf group info "Development Tools"
sudo dnf group install "Development Tools"
```

Optional extras that many projects expect but are not all mandatory in the group:

```bash
sudo dnf install cmake ninja-build gdb pkgconf-pkg-config
```

On current Fedora, `dnf` is DNF 5. `dnf group install` is the supported form; older `groupinstall` still works as a compatibility alias.

## 5. Multimedia group and extended codecs

Fedora Quick Docs install the official multimedia group with:

```bash
sudo dnf group install multimedia
```

That covers many formats shipped by Fedora.
Patent-encumbered codecs (full H.264/H.265, AAC, and so on) are not in Fedora’s own repos. After RPM Fusion is enabled, the RPM Fusion multimedia procedure is:

```bash
sudo dnf install libavcodec-freeworld x264 x265
```

If there are conflicts with other packages installed natively by Fedora, simply install this package `libavcodec-freeworld`

```bash
sudo dnf swap ffmpeg-free ffmpeg --allowerasing
sudo dnf update @multimedia --setopt="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
```

Hardware decode helpers:

- Intel (recent): `sudo dnf install intel-media-driver`
- Intel (older): `sudo dnf install libva-intel-driver`
- AMD: `sudo dnf install mesa-va-drivers-freeworld` and `sudo dnf swap mesa-vulkan-drivers mesa-vulkan-drivers-freeworld --allowerasing`
- NVIDIA (after the proprietary driver): `sudo dnf install libva-nvidia-driver`

OpenH264 for WebRTC is already available from Fedora’s Cisco repo:

```bash
sudo dnf install gstreamer1-plugin-openh264 mozilla-openh264
```

A common desktop player from RPM Fusion:

```bash
sudo dnf install vlc mpv haruna
```

## 6. Identify the NVIDIA GPU

```bash
lspci | grep -iE 'VGA|3D|NVIDIA'
mokutil --sb-state
```

Driver choice on Fedora 44, from the current [RPM Fusion NVIDIA howto](https://rpmfusion.org/Howto/NVIDIA) (the guide Fedora docs send you to):

| GPU                                                | Packages                                  |
|----------------------------------------------------|-------------------------------------------|
| Turing and newer (about 2018+, GTX 16 / RTX 20-50) | `akmod-nvidia` (595+ series on Fedora 44) |
| Maxwell and Pascal (GTX 800/900/10)                | `akmod-nvidia-580xx`                      |
| Kepler (GeForce 600/700)                           | `akmod-nvidia-470xx`                      |

The 595 driver on Fedora 44 does not support Maxwell/Pascal. Use the 580xx packages for those cards.

## 7. Secure Boot (do this before the driver if it is enabled)

If `mokutil --sb-state` reports Secure Boot enabled, sign akmods first. Fedora’s [MOK enrollment Quick Doc](https://docs.fedoraproject.org/en-US/quick-docs/mok-enrollment/) describes the enrollment screen; key creation is the RPM Fusion Secure Boot procedure:

```bash
sudo dnf install kmodtool akmods mokutil openssl
sudo kmodgenca -a
sudo mokutil --import /etc/pki/akmods/certs/public_key.der
```

Set a one-time password when asked, then reboot. On the blue MOK Manager screen: Enroll MOK, Continue, Yes, enter that password, then reboot again. Skipping this step usually produces a black screen after the NVIDIA module is installed.

If you prefer not to enroll a key, disable Secure Boot in firmware instead, then continue.

## 8. Install the NVIDIA driver from RPM Fusion

Update again and reboot if you are not already on the newest kernel.

Current GeForce / Quadro / Tesla (Turing and newer):

```bash
sudo dnf install akmod-nvidia
sudo dnf install xorg-x11-drv-nvidia-cuda
```

Maxwell / Pascal (GTX 800, 900, 10 series):

```bash
sudo dnf install akmod-nvidia-580xx xorg-x11-drv-nvidia-580xx
sudo dnf install xorg-x11-drv-nvidia-580xx-cuda
```

The CUDA subpackage is what provides `nvidia-smi` and NVENC/NVDEC. It is optional only if you do not need those.

**Wait until the module has actually been built (often under a minute, up to about five). Do not reboot immediately:**

```bash
modinfo -F version nvidia
```

A version string means the module exists for this kernel. If the command fails, wait and retry, or force the build:

```bash
sudo akmods --force
sudo dracut --force
```

Then reboot:

```bash
sudo reboot
```

## 9. Verify

```bash
nvidia-smi
lsmod | grep nouveau
modinfo -F version nvidia
```

`nvidia-smi` should list the GPU and driver version. `lsmod | grep nouveau` should print nothing. KDE Plasma on Fedora uses Wayland by default; the RPM Fusion packages enable the modeset option the Wayland session needs. In System Settings, About this System should show the NVIDIA renderer rather than llvmpipe.

Optimus laptops are handled by the same packages on current Fedora; you do not need a separate Bumblebee setup.

## 10. After a kernel update

akmods rebuilds the module on the next boot. If the desktop fails after a kernel upgrade, boot the previous kernel from GRUB, then:

```bash
sudo akmods --rebuild --force
sudo reboot
```
### Dual-GPU (laptop)

To use the NVIDIA GPU instead of the Intel integrated GPU as the primary display on Fedora 44 KDE, you must first install the proprietary drivers via RPM Fusion and then configure the display server to prioritize the discrete card.

If BIOS switching is not available or you prefer software-based switching (common for Optimus laptops), the integrated GPU (Intel/AMD) typically remains the primary display controller for power efficiency, while the NVIDIA GPU handles offloading. To force the NVIDIA GPU to drive the display session by default, you can use the following methods:

#### Set NVIDIA as Primary GPU (KDE Plasma)
KDE Plasma allows you to switch the primary GPU via an environment configuration file. Create a file in your user configuration directory:

1. Create the directory if it doesn't exist:
   ```bash
   mkdir -p ~/.config/environment.d/
   ```

2. Create or edit the file ~/.config/environment.d/90-nvidia.conf:
   ```bash
   nano ~/.config/environment.d/90-nvidia.conf
   ```

3. Add the following lines to force NVIDIA as the primary GPU:
   ```bash
   __NV_PRIME_RENDER_OFFLOAD=1
   __GLX_VENDOR_LIBRARY_NAME=nvidia
   __VK_LAYER_NV_optimus=NVIDIA_only
   ```

## Uninstall

```bash
sudo dnf remove 'xorg-x11-drv-nvidia*' 'akmod-nvidia*'
sudo reboot
```

Primary references: [Fedora Quick Docs — RPM Fusion](https://docs.fedoraproject.org/en-US/quick-docs/rpmfusion-setup/), [Fedora Quick Docs — multimedia plugins](https://docs.fedoraproject.org/en-US/quick-docs/installing-plugins-for-playing-movies-and-music/), [Fedora Quick Docs — MOK enrollment](https://docs.fedoraproject.org/en-US/quick-docs/mok-enrollment/), and [RPM Fusion NVIDIA](https://rpmfusion.org/Howto/NVIDIA) / [RPM Fusion Multimedia](https://rpmfusion.org/Howto/Multimedia).
