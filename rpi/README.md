# Connecting to  Raspberry Pi

There are at least two ways to connect to Raspberry Pi:
* Directly to the GPIO serial lines, or
* Via the Teensy USB interface.

For this I'm going via Teensy, because of the various
line-discipline things implemented there in firmware
(case conversion, and so on).  But I think the direct 
GPIO serial connection should work fine too.  In that
case the port would be ttyAMA0 instead of ttyACM0 below.

You can see all the ports using `ls /dev/tty*` 

## getty on systemd

Recent versions of raspbian use `systemd`.
To request a getty on the 'ttyACM0' device,
```
sudo systemctl start getty@ttyACM0.service
```

To have this start automatically at boot,
```
sudo systemctl enable getty@ttyACM0.service
```
Now a `reboot` should immediately print the login message.

You can see all the getty properties using
```
sudo systemctl show getty@ttyACM0.service
```


### Auto-login

Create a directory,
```
sudo mkdir /etc/systemd/system/getty@ttyACM0.service.d
```

Then create a file `/etc/systemd/system/getty@ttyACM0.service.d/override.conf` with
contents:
```
[Service]
Type=simple
ExecStart=
ExecStart=-/sbin/agetty --nohostname --autologin username --noclear ttyACM0 tty33
```
(Yes, there are two `ExecStart=` lines).
The `tty33` is the terminal type (defined in `/lib/terminfo/`) and
gives you a plain terminal without escape sequences for colors.

You may need to set `stty brkint` for BREAK to send Ctrl+C to the host.


