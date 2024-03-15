# Rocky8 KVM with GPU Pass through

Unless NVidia GPU supports vGPU, only one VM at a time can use the pass-through configuration.\
For vGPU compatible NVidia Cards see [](https://docs.nvidia.com/grid/gpus-supported-by-vgpu.html)
## Enable IOMMU
```Shell
sudo vim /etc/default/grub
```
Content
```Shell
# line "GRUB_CMDLINE_LINUX" : add at the end
# * for Intel CPU add "intel_iommu=on"
# * for AMD CPU, add "amd_iommu=on"
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/rl-swap rd.lvm.lv=rl/root rd.lvm.lv=rl/swap rhgb quiet intel_iommu=on"
GRUB_DISABLE_RECOVERY="true"
GRUB_ENABLE_BLSCFG=true
```
Rebuild GRUB Config
```Shell
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```
## Configure `vfio`
Get GPU device IDs
```Shell
sudo lspci -nn | grep -i nvidia
```
```Shell
03:00.0 VGA compatible controller [0300]: NVIDIA Corporation GF106GL [Quadro 2000] [10de:0dd8] (rev a1)
03:00.1 Audio device [0403]: NVIDIA Corporation GF106 High Definition Audio Controller [10de:0be9] (rev a1)
```
> NOTE `device ids` and `vfio-pci ids`:\
> GPU—`03:00.0` and `10de:0dd8`\
> GPU Audio—`03:00.1` and `10de:0be9`

Create new `vfio.conf` file with `vfio-pci ids`
```Shell
sudo vim /etc/modprobe.d/vfio.conf
```
content
```Shell
options vfio-pci ids=10de:0dd8,10de:0be9
```
Create `vfio-pci.conf` file
```Shell
sudo vim /etc/modules-load.d/vfio-pci.conf
```
Content 
```Shell
vfio-pci
```
reboot

Verify IOMMU is on
```Shell
sudo dmesg | grep -E "DMAR|IOMMU"
```
```Shell
[    0.000000] ACPI: DMAR 0x00000000000FD2D3 000110 (v01 DELL   B10K     00000015 ASL  00000061)
[    0.000000] ACPI: Reserving DMAR table memory at [mem 0xfd2d3-0xfd3e2]
[    0.000000] DMAR: IOMMU enabled
[    0.001000] DMAR: Host address width 40
[    0.001000] DMAR: DRHD base: 0x000000dfffe000 flags: 0x0
[    0.001000] DMAR: dmar0: reg_base_addr dfffe000 ver 1:0 cap c90780106f0462 ecap f020fe
[    0.001000] DMAR: DRHD base: 0x000000fedc0000 flags: 0x1
[    0.001000] DMAR: dmar1: reg_base_addr fedc0000 ver 1:0 cap c90780106f0462 ecap f020fe
[    0.001000] DMAR: RMRR base: 0x000000dbe58000 end: 0x000000dbe6ffff
[    0.001000] DMAR: ATSR flags: 0x0
[    0.001000] DMAR: ATSR flags: 0x0
[    0.001000] DMAR-IR: IOAPIC id 10 under DRHD base  0xdfffe000 IOMMU 0
[    0.001000] DMAR-IR: IOAPIC id 9 under DRHD base  0xfedc0000 IOMMU 1
[    0.001000] DMAR-IR: IOAPIC id 8 under DRHD base  0xfedc0000 IOMMU 1
[    0.001000] DMAR-IR: Enabled IRQ remapping in xapic mode
[    1.349965] DMAR: No SATC found
[    1.349969] DMAR: dmar0: Using Queued invalidation
[    1.349983] DMAR: dmar1: Using Queued invalidation
[    1.352113] DMAR: Intel(R) Virtualization Technology for Directed I/O
```
>NOTE `[ 0.000000] DMAR: IOMMU enabled`—this line should be there

Confirm vfio enabled
```Shell
sudo dmesg | grep -i vfio
```
```Shell
[    5.531363] VFIO - User Level meta-driver version: 0.3
[    5.557285] vfio_pci: add [10de:0dd8[ffffffff:ffffffff]] class 0x000000/00000000
[    5.569059] vfio_pci: add [10de:0be9[ffffffff:ffffffff]] class 0x000000/00000000
```
>NOTE: no errors should be displayed in the above output