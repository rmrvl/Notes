# Launch qemu-kvm virtual machine

## Dedicated Network Interface

### Create the Bridge Interface

We want to create a Bridge interface that will be named `br0` with the ipv4 address range of `10.0.0.0/24`.
The ip address of the bridge interface will be `10.0.0.1`.
One bridge interface will correspond to one VM network.

```
ip link add br0 type bridge
ip link set br0 up
ip addr add 10.0.0.1/24 brd + dev br0
```

### Create the Tunnel Interface

This is the interface that the VM will be connected to : `tap0`
It will be linked to the bridge interface that we created earlier.

```
ip tuntap add dev tap0 mode tap
ip link set tap0 master br0
ip link set tap0 up
```

This step has to be run each time a new VM is created.

### Run Qemu using the network interface
You can then create the Virtual Machine using KVM.

`qemu-system-x86_64 -hda vm_disk.vdi -m 2G -enable-kvm -netdev tap,id=network0,ifname=tap0 -device e1000,netdev=network0,mac=de:ad:be:ef:00:ff -nographic`

In this example, the VM will be created using the hard drive vm_disk.vdi.
It will be allocated 2GB of RAM and it will use the `tap0` interface.
The MAC address of this VM will be `DE:AD:BE:EF:00:ff`

### Remove a VM interface

Each time the VM is removed, delete the associated tunnel interface

```
ip link set tap0 down
ip link del tap0
```

### Remove the VM network interface

The bridge can be removed when each VM has been deleted.

```
ip link set br0 down
ip link del br0
```

## Connect VM on Docker Container Network

### Connect the Tunnel Interface to Docker Bridge Interface

This will allow your VM to be on the direct network of your docker containers.
It will use the `docker0` interface.

```
ip tuntap add dev tap0 mode tap
ip link set tap0 master docker0
ip link set tap0 up
```

Then you will need to setup manually the ip address of your VM inside the docker ip range.
If `docker0` interface has the ip `10.0.0.1/24`

```
ip addr add 10.0.0.2 dev eth0
ip route add default via 10.0.0.1
```

## Using User-Mode Networking

It is possible to use port forwarding to host a service on a VM using user-mode network interface

```
virsh qemu-monitor-command --hmp VM_NAME 'hostfwd_add ::HOST_PORT-:GUEST_PORT'
```
