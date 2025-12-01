# Dell XPS 13 (9320) IPU6 Webcam Fix - Ubuntu 25.10

**Date:** December 1, 2025
**Hardware:** Dell XPS 13 Plus 9320 (Intel IPU6 Camera)
**OS:** Ubuntu 25.10 (Kernel 6.17)

## The Problem

The Intel IPU6 camera is not a standard USB UVC device; it requires a complex middleware stack (Intel IPU6 drivers + GStreamer).

  * **Symptom:** The `v4l2-relayd` service is active, but Zoom/Browsers do not show a "Virtual Camera," or they show it but get a black screen.
  * **Diagnostic:** `v4l2-ctl -d /dev/video0 --list-formats` returns an empty list (no formats negotiated), indicating the relay service failed to attach to the loopback device.
  * **Root Cause:** The default `v4l2-relayd` wrapper script is fragile. It often crashes due to syntax errors (undefined `SPLASHSRC`) or capability negotiation failures, leaving the loopback device in a zombie state.

## The Solution: The "Manual Pipe" Method

Instead of relying on the buggy `v4l2-relayd` auto-sensing logic, we disable it and create a simple, bulletproof systemd service that pipes the camera directly to the virtual node. We then control it via Bash aliases to manage the LED.

### 1. Disable the Default Service

Prevent the broken service from fighting for the device.

```bash
sudo systemctl stop v4l2-relayd
sudo systemctl disable v4l2-relayd
sudo systemctl mask v4l2-relayd
```

### 2. Create the Custom Service

Create a new file: `/etc/systemd/system/ipu6-stream.service`

```ini
[Unit]
Description=IPU6 Custom Webcam Stream
After=network.target sound.target

[Service]
Type=simple
# 1. Load the loopback module with explicit capabilities
# exclusive_caps=1 is required for Chrome/Zoom to detect it as a Webcam
ExecStartPre=/sbin/modprobe v4l2loopback exclusive_caps=1 card_label="Virtual Camera"

# 2. The GStreamer Pipeline
# We pipe the raw IPU6 source (icamerasrc) directly to the loopback sink (v4l2sink)
# We force NV12 format to minimize CPU usage
ExecStart=/usr/bin/gst-launch-1.0 icamerasrc ! "video/x-raw,format=NV12,width=1280,height=720" ! videoconvert ! v4l2sink device=/dev/video0

# 3. Restart logic
# If the stream dies (suspend/resume), restart it automatically
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

### 3. Create User Controls (Bash Aliases)

Because this service bypasses the auto-sensing logic, the camera LED will be ON whenever the service is running. We use aliases to toggle it manually.

Add to `~/.bashrc` (or `~/.zshrc`):

```bash
# XPS 13 Camera Controls
alias cam-on='sudo systemctl start ipu6-stream'
alias cam-off='sudo systemctl stop ipu6-stream'
```

*Reload config:* `source ~/.bashrc`

### 4. Verification

To verify the fix is working, start the camera and check the format list.

```bash
cam-on
v4l2-ctl -d /dev/video0 --list-formats
```

**Success Criteria:** The output **must** show the NV12 format:
`[0]: 'NV12' (Y/UV 4:2:0)`

-----

## Troubleshooting / Hard Reset

If the camera ever freezes or refuses to start (e.g., after a kernel update or bad suspend), run this sequence to fully flush the driver stack:

```bash
# 1. Stop all video processes
sudo systemctl stop ipu6-stream
sudo killall gst-launch-1.0

# 2. Force-unload the kernel module (destroys /dev/video0)
sudo modprobe -r v4l2loopback

# 3. Reload the module clean
sudo modprobe v4l2loopback exclusive_caps=1 card_label="Virtual Camera"

# 4. Restart the stream
sudo systemctl start ipu6-stream
```

-----

## Why this approach is better for Developer Edition

While the "smart" service is nice in theory, the IPU6 architecture is complex. By using `gst-launch-1.0` directly in a systemd unit:

1.  **Observability:** You can see exactly why it fails in `journalctl`.
2.  **Stability:** It removes the fragile wrapper script logic.
3.  **Privacy:** You have absolute confirmation of when the camera is powered (LED state corresponds strictly to the service state).
