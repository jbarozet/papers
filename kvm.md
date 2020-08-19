# KVM

## Install on ArchLinux

```bash
# yay -S qemu virt-manager ebtables 
```

Start systemd service:
```bash
# sudo systemctl start libvirtd 
# sudo systemctl enable libvirtd 
```

Add current user to libvirtd group
```bash
# sudo usermod -G libvirtd -a jmb
```

If you use a Window Manager like dwm, spectrwm, awesome, etc, check also polkit on Arch. You will need one of them with WM (lxsession).
With Desktop environment like Gnome/KDE this is ok.






