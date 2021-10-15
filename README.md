# Fusion M1 NixOS w/ Kernel 5.14 Issue

This repository reproduces an issue I'm seeing: when installing NixOS
with Linux Kernel 5.14, the VM spins to 100% CPU on boot (but never makes
it past what looks like the EFI screen _perhaps_). This only happens on
Fusion Public Tech Preview on Apple M1, and does NOT happen on Fusion 12
on Intel.

This matters because `open-vm-tools` requires at least kernel 5.14 for
GUI support and that matters for my use case.

## Requirements

* Apple computer with an Apple M1 chip
* [VMware Fusion for Apple Silicon Public Tech Preview](https://blogs.vmware.com/teamfusion/2021/09/fusion-for-m1-public-tech-preview-now-available.html)
* Docker Desktop to build the NixOS aarch64 ISO

## Steps to Reproduce

**All of these steps must be run on an M1 Mac computer.** If you run it
on an Intel machine there is _no bug_.

1. `make nixos.iso` - This will create the NixOS ISO. If you want to
  recreate the ISO you must delete the old one. This will place the ISO
  in the current directory as `nixos.iso`.

2. Create a VM in Fusion (manually). Settings I used:

  * From `nixos.iso`
  * 4 CPUs, 8192 RAM
  * 150 GB disk, SATA **important: switch NVMe to SATA!**
  * Full resolution display

3. Boot it up. You'll log in automatically as the `nixos` user. Go to
  the root user and set the password to "root":
  
```
$ sudo su
$ passwd
```

4. Get the IP address, should be `192.168.something` from `ifconfig`.

5. **Optional:** Take a snapshot here if you want to try this multiple times.

6. Set `NIXADDR=<ip address of VM>` and run `make vm/bootstrap-broken`. This
  will reproduce the bug and automatically reboot the VM. Your CPU should
  spin to 100% (or 100% times the number of vCPUs). This process takes maybe
  10 minutes until the reboot.

7. **Optional:** You can restore from the snapshot and run `make vm/bootstrap-good`
  which uses a 5.10 kernel which _works_ and does not bug out.
