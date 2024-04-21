# GPU passthrough

## Kernel Modules

Add the following `GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on iommu=pt video=efifb:off"` in **/etc/default/grub**
Load the new grub-config using `sudo update-grub` then reboot the machine

## Installing the VM

Install virt-manager and into edit/preferences allow the xml edit feature.

`sudo apt-get install virt-manager`

Create a new windows VM using virt-manager. This VM needs to run under system connection.
Before running the install go to custom setup to check the different sections.

### Overview
Check that it uses KVM Hypervisor, Q35 chipset and UEFI Firmware.

### CPU
Assign Cores for your VM and configure the host-passthrough and define the topology for the VM.
For instance : `<topology sockets="1" dies="1" cores="2" threads="4"/>`

### NIC (Optional)
You can remove the network interface to do an offline install

Then check that the VM starts and runs.


## Prerequisites

### Check IOMMU groups
Check your IOMMU groups to ensure that the passthrough can be done using the **iommu.sh** script.

Your GPU should be alone in its IOMMU group. If you have *Host bridge* and *PCI bridge* in your GPU IOMMU group; **It is not a problem**, the passthrough will still work.

### Setup Remote Control
Edit the **Spice Display** Section with listen type by address and set the value to **all interfaces**.
That way, you will be able to access you VM through an other pc on your network using the port *5900*.

### Install Virtio

Check the [Proxmox Virtio Wiki](https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers).
The ISO can be downloaded from [Fedora people](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D)

It will allow you to use virtio devices for the SATA disk section and the NIC disk section

### Configuring Libvirt Hook
Copy the **qemu.hook** script in */etc/libvirt/hooks* and name it **qemu** and set execute flat `chmod 755 /etc/libvirt/hooks/qemu`

Add the your VM in the hooked list `HOOKED_VMS=("vm1" "vm2")`

### Passing the GPU to the VM

Get the pci bus id from `lspci` and override values into the qemu hook script.
For example, if it return 21:00:1 for your GPU video, then put `VIRSH_GPU_VIDEO=pci_0000_21_00_1` into the hook script.

If your GPU has more PCI devices, add them into the script and add the corresponding nodedev-detach and nodedev-reattach commands.

## Installing the GPU

Launch the VM. The hook will unload the GPU drivers which will make you lose the screens. Use the second machine to acces you VM through the Spice Remote Protocol and install the GPU drivers in your VM.

The screens should turn on again on the VM which mean that the GPU is used by the VM.

## Finish the VM configuration

Now that the VM is able to use the GPU, remove the Channel Spice, Spice Display, Video QXL, USB Redirectors and Sound ich Channels.

Remove also the \<audio\> tag in the VM xml.

Add back a network interface to your VM.

## Hide Virtualization

In order to hide the virtualization, it is better to directly edit the XML descriptor of the VM : `virsh edit VM`

### Activate KVM hidden feature

Under `<features>` tag, add the following `<kvm><hidden state='on'/></kvm>`

### Add Vendor ID to hypervisor

Under `<features><hyperv>` tags add `<vendor_id state='on' value='00112233'/>` where the value **00112233** can be put to any random value you want.

### Disable Hypervisor in virtual CPU

Under `<cpu>` tag, add `<feature policy='disable' name='hypervisor'/>`

### Set IOAPIC

**Optional**: add `<ioapic driver='kvm'/>` under `<features>` tag.

### Set the System Management BIOS

Under `<cpu>` tag, add `<smbios mode='host'/>` to use the one of the host.

However, you can create one from scratch, as shown in [libvirt documentation](https://libvirt.org/formatdomain.html#smbios-system-information)

### Change MAC Address

Change the MAC Address of your virtual network interface to use a true one.
