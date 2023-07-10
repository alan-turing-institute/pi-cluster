---
title: "Notes for setting up Pi 4"
---

# NixOS installation

I (JG) would like to try NixOS as it provides a declarative
configuration for the OS, which seems a good thing when dealing with
multiple systems.

## Preamble

The Pi boots from an SD card, which it also uses as its “disk
space”. To install any OS on a Pi one needs to write a boot image to
the card. However, by default our work laptops block access to
removable storage.

However, I happen to have an existing Pi running Ubuntu. I also have
an SD card reader in a USB A stick. So I plan to use my Pi to
download and write a NixOS SD image.

## Getting NixOS

The installation methods on the NixOS home page[^1] provide CD
images not SD card images. However, 

[^1]: https://nixos.org/
