---
title: "Setting up NixOS on a Pi 4"
---

These instructions summarise setting up NixOS on a Pi, starting from a
generic NixOS distribution. It is a summary of the raw notes.

The main steps are:

1. Install a generic NixOS SD 
2. Boot to console and get network access
3. Update
4. Other configuration

The primary challenge is that by default `sshd` is not enabled. In
order to enable `sshd` you will need (a) console access (ie, a screen
and keyboard) and (b) internet access.

# Setup

## 1. Install generic OS

From the latest build at:
https://hydra.nixos.org/job/nixos/trunk-combined/nixos.sd_image.aarch64-linux

Figure out which device your SD card is (eg, `/dev/sda` on Linux or
`/dev/disk4` on my Mac)
```sh 
unzstd <the downloaded image.zst>
sudo dd if=nixos-sd-image-XXX-aarch64-linux.img of=/dev/sda bs=4096 conv=fsync status=progress
```
(On the Macbook at least, using, eg, `bs=65536` speeds up the transer.)

## 2. Get network access 

If you have wired ethernet access you can skip the next
step. Otherwise, you'll need to set up wifi.

As root:
```sh
systemctl start wpa_supplicant
wpa_cli
```

If you want to check that you can see your wifi:
```
> scan
> scan_results
```

and then, to connect:
```
> add_network
Will probably say 0 here
> set_network 0 ssid "<your network name>"
> set_network 0 psk "<your network password>"
> enable_network 0
> quit
```

With networking up, you can rebuild the OS. Add a config file:
```sh
nixos-generate-config
```

Uncomment the appropriate line to enable ssh. Also, I *think* you need
to add a user and set a username for that user. (If you don't add any
users, you won't be able to log in.) I added the `nixos` user. I am
not sure what happens if you add a different one. 

Important: now use `passwd` to set a password for the `nixos` user, 

If you need to stay on wifi, uncomment `networking.wireless.enable = true;` and add:
```nix
networking.wireless.networks = {
  "<your network name>" = {
    psk = <your network password>;
  };
};
```

And rebuild:
```sh
nixos-rebuild boot
```

## 3. Update 

Update Pi firmware: 
```sh
$ nix-shell -p raspberrypi-eeprom
$ sudo mount /dev/disk/by-label/FIRMWARE /mnt
$ sudo BOOTFS=/mnt FIRMWARE_RELEASE_STATUS=stable rpi-eeprom-update -d -a
```

Upgrade Nixos:

```sh
$ nixos-rebuild boot --upgrade
```

(I also switched channels to `nixos-unstable-small`.)

Add the hardware channel:
```
$ sudo nix-channel --add https://github.com/NixOS/nixos-hardware/archive/master.tar.gz nixos-hardware
$ sudo nix-channel --update
```

and add
```nix
imports = [
  <nixos-hardware/raspberry-pi/4>
  ./hardware-configuration.nix
];
```


## 4. 
