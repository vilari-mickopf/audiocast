# Audiocast

Streaming audio over network using pulseaudio module-rtp-send/module-rtp-recv.

Set of small scripts for effortless loading/unloading modules from both sides simultaneously.
Server will load/unload module-rtp-send and send start/stop commands ovet tcp port that was previosly opened by receiver.
Receiver will continuously wait on the port for commands from the server which will load/unload module-rtp-recv.

# Getting Started

## Requirements

Following packages are needed on both server and receiver sides:
- pulseaudio
- pulseaudio-rtp
- nmap
- netcat

```bash
pacman -S pulseaudio pulseaudio-rtp nmap netcat
```

## Installation

### Server

Server argument will add rtp sink to default.pa and install audiocast script

```bash
./install server
```

### Receiver

Receiver argument will only install audiocast script

```bash
./install receiver
```

## Configuration

Port used for communication between server and receiver can be found in
$XDG_CONFIG_HOME/audiocast/config file.

```ini
port=5212
```

Use same 'port' value on server and receiver side.

# Usage

## Server

- Start streaming:
```bash
audiocast start
```

- Stop streaming
```bash
audiocast stop
```

If no argument is provided, audiocast will determine the action based on current
sink. If the current sink is rtp it will start streaming, otherwise it will stop.

```bash
audiocast
```

In my i3 config I have this option which is useful for starting and stopping the
stream with one same key:

```bash
bindsym $mod+Shift+m exec --no-startup-id changesink && audiocast
```
- [changesink](https://github.com/vilari-mickopf/dotfiles/blob/master/.config/i3/scripts/changesink) script


## Receiver

Audiocast on receiver side should continuously wait on port for server's command:

```bash
audiocast
```

Start with systemd instead:

```bash
systemctl --user start audiocast
```

To start on boot:

```bash
systemctl --user enable audiocast
```

_Note_: Use --user, because running pulseaudio in system wide mode is not advisable.
Make sure that pulseaudio is running on both server and receiver sides:
```bash
systemctl --user start pulseaudio
systemctl --user enable pulseaudio
```
