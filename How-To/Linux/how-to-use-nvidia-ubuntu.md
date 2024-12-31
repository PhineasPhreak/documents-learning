# How to use the `prime` commande in Ubuntu

PRIME render offload is a technology that allows you to use an integrated GPU for display output while selectively offloading rendering of certain applications to a discrete NVIDIA GPU. Here's how to use the different PRIME-related tools:

## PRIME Render Offload

To use PRIME render offload:

1. Ensure you have a recent NVIDIA driver (435.17+) installed.
2. Configure your system to use the integrated GPU for display:

```bash
Section "ServerLayout"
    Identifier "layout"
    Option "AllowNVIDIAGPUScreens"
EndSection
```

3. To run an application using the NVIDIA GPU:

```bash
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia application_name
```

For example:

```bash
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears
```

You can also use the `prime-run` script if available:

```bash
prime-run glxgears
```

## prime-select

`prime-select` is used on some distributions to switch between different GPU modes:

- To switch to NVIDIA GPU:
  ```bash
  sudo prime-select nvidia
  ```

- To switch to integrated GPU:
  ```bash
  sudo prime-select intel
  ```

- To check current mode:
  ```bash
  prime-select query
  ```

## prime-switch

`prime-switch` is less common but may be available on some systems. It typically performs a similar function to `prime-select`:

```bash
sudo prime-switch nvidia
sudo prime-switch intel
```

## Key Points

- PRIME render offload allows per-application GPU selection without rebooting.
- `prime-select` and `prime-switch` usually change the system-wide GPU configuration and may require a logout or reboot to take effect.
- For optimal power management, consider enabling Dynamic Power Management (DPM) if supported by your hardware and drivers[4].
- Some desktop environments like GNOME may require additional tweaks for PRIME offloading to work correctly with fullscreen applications[4].

Remember to check your specific distribution's documentation, as the exact commands and configurations may vary slightly between different Linux distributions.
