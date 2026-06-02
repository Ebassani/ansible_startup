# Fedora Workstation Ansible Setup

This playbook is tuned for a Fedora Workstation install on a hybrid AMD iGPU + NVIDIA laptop such as your Acer Nitro 16.

## First run

Install Ansible and the Flatpak collection:

```bash
sudo dnf install -y ansible
ansible-galaxy collection install -r requirements.yaml
```

Run the setup:

```bash
ansible-playbook -i inventory.ini playbook.yaml --ask-become-pass
```

Reboot after the NVIDIA block finishes. The RPM Fusion `akmod-nvidia` package builds a kernel module for your current kernel, and the runtime tests are much more meaningful after reboot.

## Post-reboot tests

```bash
ansible-playbook -i inventory.ini tests.yaml --ask-become-pass
```

Useful targeted runs:

```bash
ansible-playbook -i inventory.ini playbook.yaml --ask-become-pass --tags nvidia
ansible-playbook -i inventory.ini playbook.yaml --ask-become-pass --tags flatpak
ansible-playbook -i inventory.ini playbook.yaml --ask-become-pass --tags validation
```

## Secure Boot note

If Secure Boot is enabled and NVIDIA does not load after reboot, the NVIDIA module probably needs Machine Owner Key enrollment/signing. Fedora 41+ improved NVIDIA installation with Secure Boot support through GNOME Software, but manual RPM Fusion installs can still require extra akmods signing steps depending on the install path and firmware state.


## Tailscale

The playbook installs `tailscale` and enables `tailscaled`, but it does not log you into your tailnet or force an exit node.

After the first boot, authenticate with:

```bash
sudo tailscale up
```

To use an existing exit node:

```bash
tailscale status
sudo tailscale set --exit-node=<exit-node-name-or-tailscale-ip> --exit-node-allow-lan-access=true
```

To stop using the exit node:

```bash
sudo tailscale set --exit-node=
```
## Acer Nitro extras

This playbook handles Fedora-side power profiles through `tuned`/`tuned-ppd` and hybrid graphics helpers. It does not install unofficial Acer Nitro keyboard RGB/fan kernel modules. Those are model-specific and should be added only after the base system and NVIDIA driver are confirmed stable.

## Package notes

The NVIDIA list is intentionally slim:

- `kernel-devel-matched` helps akmods build against the matching Fedora kernel.
- `akmod-nvidia` is the main RPM Fusion NVIDIA driver package.
- `xorg-x11-drv-nvidia-cuda` provides NVIDIA compute/video tooling such as `nvidia-smi`.
- `xorg-x11-drv-nvidia-power` improves NVIDIA laptop power management.
- `nvidia-settings` is the NVIDIA settings GUI.
- `libva-nvidia-driver` supports VA-API video acceleration through the NVIDIA driver.
- `vulkan-tools` is mainly for post-install testing with `vulkaninfo`.

The multimedia list is only for common Fedora codec gaps and AMD iGPU VA-API support. `libdvdcss`, VDPAU diagnostics, and native 32-bit Steam/Lutris libraries are left out for now.

`net.davidotek.pupgui2` is ProtonUp-Qt. It manages Proton-GE and Wine-GE compatibility tools for Steam, Lutris, Heroic, and similar launchers. Remove it from `flatpaks` if you do not play Windows games through Proton.

// TODO:
Move steam from flatpack to rpm,
Do the same for jetbrains products