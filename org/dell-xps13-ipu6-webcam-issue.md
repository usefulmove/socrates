# Dell XPS 13 (9320) IPU6 Webcam Fix - Ubuntu 25.10

**Date:** December 1, 2025
**Hardware:** Dell XPS 13 Plus 9320 (Intel IPU6 Camera)
**OS:** Ubuntu 25.10 (Kernel 6.17)

## The Problem

The Intel IPU6 camera is not a standard USB UVC device; it requires a complex middleware stack. The default Ubuntu drivers (`v4l2-relayd`) often crash or fail to negotiate with apps like Zoom, resulting in a black screen or "No Device Found."

## The Solution: Manual Pipeline Service

We bypass the fragile "auto-sensing" service and use a raw GStreamer pipeline managed by systemd. We control the camera (and the LED) manually via Bash aliases to ensure privacy and battery life.

### 1. Disable the Default/Broken Service

Prevent the bundled service from fighting for the device.

```bash
sudo systemctl stop v4l2-relayd
sudo systemctl disable v4l2-relayd
sudo systemctl mask v4l2-relayd
```

### 2. Configure the Driver Name (Optional)

Ensure the camera shows up as "Virtual Camera" instead of the generic "Intel MIPI Camera" to avoid confusion with the raw hardware nodes.

```bash
echo 'options v4l2loopback exclusive_caps=1 card_label="Virtual Camera"' | sudo tee /etc/modprobe.d/v4l2loopback.conf
```

### 3. Create the Custom Service

Create file: `/etc/systemd/system/ipu6-stream.service`

```ini
[Unit]
Description=IPU6 Custom Webcam Stream
After=network.target sound.target

[Service]
Type=simple
# Load the loopback module (if not already loaded)
ExecStartPre=/sbin/modprobe v4l2loopback

# The Pipeline: Pipe raw camera (NV12) -> Loopback Sink
ExecStart=/usr/bin/gst-launch-1.0 icamerasrc ! "video/x-raw,format=NV12,width=1280,height=720" ! videoconvert ! v4l2sink device=/dev/video0

# Restart automatically if the stream crashes (e.g. after suspend)
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

### 4. Disable Auto-Start (Crucial)

We **do not** want the camera (and LED) to turn on automatically at boot. We want it to stay off until we ask for it.

```bash
# Reload systemd to see the new file
sudo systemctl daemon-reload

# Ensure it is NOT set to start at boot
sudo systemctl disable ipu6-stream
```

### 5. Create User Controls

Add these aliases to your `~/.bashrc` or `~/.zshrc` to toggle the camera on demand.

```bash
# XPS 13 Camera Controls
alias cam-on='sudo systemctl start ipu6-stream'
alias cam-off='sudo systemctl stop ipu6-stream'
```

*Apply changes:* `source ~/.bashrc`

### 6. Workflow

  * **Default State:** Camera & LED are **OFF**.
  * **Join Meeting:** Run `cam-on`. LED turns on. Select "Virtual Camera" in Zoom.
  * **End Meeting:** Run `cam-off`. LED turns off.

### Troubleshooting

If the camera ever freezes (e.g., after a kernel update or bad suspend), run this "Hard Reset" sequence:

```bash
sudo systemctl stop ipu6-stream
sudo killall gst-launch-1.0
sudo modprobe -r v4l2loopback
sudo modprobe v4l2loopback
sudo systemctl start ipu6-stream
```
