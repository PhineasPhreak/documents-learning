Here is a detailed guide on how to configure your Linux server (running Ubuntu or Fedora) to prevent system suspension when the laptop lid is closed, thereby keeping the Wi-Fi connection active.

This solution focuses on modifying the `systemd-logind` service, which manages user session events like lid closures and power states.

---

# Solution 1: Ignoring Lid Switch Events

The cleanest method to achieve your goal is to instruct `systemd-logind` to completely ignore any signals related to the laptop lid switch, preventing it from triggering a system suspend or sleep action.

## Step 1: Inspect the Current Configuration

Before making any changes, it is good practice to review the existing configuration file to understand the current behavior and identify commented-out settings.

Execute the following command in your terminal:
```bash
grep -E '^[#]*HandleLidSwitch' /etc/systemd/logind.conf
```
This command will display any lines in `/etc/systemd/logind.conf` that relate to lid switch handling, showing whether they are currently enabled or commented out.

## Step 2: Edit the Configuration File

You need to modify the main logind configuration file. Use a text editor like `nano` to make the required changes:
```bash
sudo nano /etc/systemd/logind.conf
```

Inside this file, locate the lines related to lid switch handling. You will likely find configurations similar to these (often commented out with a `#`):

```ini
#HandleLidSwitch=suspend
#HandleLidSwitchExternalPower=suspend
#HandleLidSwitchDocked=ignore
```

**Modify these lines** to explicitly tell `systemd-logind` to ignore all lid switch events by setting them all to `ignore`:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

**Save the changes:**
1.  Press **Ctrl+O** to write out (save) the file.
2.  Press **Enter** to confirm the filename.
3.  Press **Ctrl+X** to exit the editor.

## Step 3: Apply the Changes by Rebooting

Since this change affects a core system service, a complete system reboot is the most reliable way to ensure `systemd-logind` reloads the new configuration correctly. Performing a remote restart of `systemd-logind` is generally discouraged on servers; a full reboot ensures all dependencies are initialized properly.

Execute the following command:
```bash
sudo reboot
```

## Step 4: Verification

After the server has fully restarted, test your setup:
1.  Close the laptop lid.
2.  Wait a few moments for the system to settle.
3.  Attempt to connect via SSH from another machine using your configured port (e.g., `ssh -p 2222 username@your-server-ip`).

If the connection is maintained, the configuration was successful.

---

# Advanced Configuration: Permanent 24/7 Server Setup

For a true 24/7 server laptop environment, relying solely on ignoring the lid switch might not cover all power management concerns. A more robust solution involves configuring the network manager and power management services to maintain connectivity when the machine enters a low-power state or is closed.

This advanced setup generally requires configuration in two areas: **Network Manager settings** (to handle Wi-Fi reconnection) and potentially **Power Management profiles**.

## Key Goals for Advanced Setup:

1.  **Automatic Wi-Fi Reconnection:** Ensure that when the lid closes (or power state changes), the network interface does not drop its connection but attempts to reacquire the signal automatically once the system is woken up.
2.  **Disable Wi-Fi Power Saving:** Prevent the Wi-Fi adapter from entering deep sleep modes that could sever the connection entirely.
3.  **Sustained SSH Access:** Ensure that the network interface remains active and accessible for remote management (SSH) even when the physical machine is closed.

## Implementation Considerations:

While specific commands depend heavily on your exact Ubuntu/Fedora version and network setup (e.g., using `NetworkManager` vs. older methods), you should investigate configuration files related to:

*   **Network Manager Configuration:** Look for profiles or settings that control interface behavior during suspend/resume cycles. You may need to set specific flags to prevent the device from power-saving aggressively.
*   **Systemd Sleep/Suspend Settings:** Review systemd configuration units related to power management (`sleep.target`, etc.) to ensure they do not override your desired network stability settings.

**Recommendation:** If you require this advanced setup, search for documentation specific to **"Ubuntu NetworkManager lid close suspend"** or **"Fedora power management Wi-Fi"** as the exact configuration steps are highly dependent on the specific distribution and hardware drivers installed.
