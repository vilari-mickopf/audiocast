# Audiocast

Streaming audio over network using pulseaudio module-rtp-send/module-rtp-recv.

Set of small scripts for effortless loading/unloading modules from both sides simultaneously.
It provides easier control of the stream using tcp port for communication between server and receiver.

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

Server argument will add rtp sink to default.pa and install audiocast server script

```bash
./install server
```

### Receiver

Receiver argument will only install audiocast receiver script

```bash
./install receiver
```

## Configuration

Port used for communication between server and receiver can be found in
$XDG_CONFIG_HOME/audiocast/config file.

```ini
port=5212
```

Use same 'port' value on server and receiver side

# Usage

## Server

- 'start' will load rtp-module if it's not already loaded
```bash
audiocast start
```

- 'stop' will unload rtp-module
```bash
audiocast stop
```

- 'restart' will unload and then load rtp-module
```bash
audiocast stop
```

If no argument is provided, audiocast will determine the action based on current
sink. If the current sink is rtp it will use 'start' command, otherwise it will exit.

```bash
audiocast
```

In my i3 config I have this option which is useful for starting and stopping the
stream with one same key:

```bash
bindsym $mod+Shift+m exec --no-startup-id changesink && audiocast
bindsym $mod+Ctrl+m exec --no-startup-id audiocast restart
```
- [changesink](https://github.com/vilari-mickopf/dotfiles/blob/master/.config/i3/scripts/changesink) script


## Receiver

Audiocast on receiver side should continuously wait on port for server's command.
Received commands will mirror the actions of the server, i.e. 'start'
will load rtp-module if it's not already loaded, 'stop' will unload the module,
and 'restart' will unload and then load the module.

```bash
audiocast
```

Start with systemd instead:

```bash
systemctl --user start audiocast
```

Start automatically on login:

```bash
systemctl --user enable audiocast
```

_Note_: Use --user, because running pulseaudio in system wide mode is not advisable.
Make sure that pulseaudio is running on both server and receiver sides.
