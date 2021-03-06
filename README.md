# kvmtop [![Build Status](https://travis-ci.org/cha87de/kvmtop.svg)](https://travis-ci.org/cha87de/kvmtop)

## What kvmtop does
It reads utilisation metrics about virtual machines
running on the KVM hypervisor from different sources:
 - the Linux /proc filesystem
 - using the virsh [1] tool

Why yet another monitoring tool for virtual machines?

The CPU measurements are read directly from the hypervisors Linux kernel. kvmtop takes
into account the difference between CPU utilisation inside and outside the virtual machine,
which will differ e.g. in cases of cpu over provisioning. kvmtop also collects 
utilisation values of the hypervisor for virtual machines, to measure the overhead needed
to run a virtual machine.

[1] http://linux.die.net/man/1/virsh

## Usage

```
kvmtop --help
Usage of bin/kvmtop:
  -batch
    	use simple output e.g. for scripts (default: false)
  -cpu
    	show cpu (default: true) (default true)
  -disk
    	show disk  (default: false)
  -memory
    	show memory (default: false)
  -network
    	show network (default: false)
  -qemu-binary-name string
    	binary name of qemu driver. default qemu-kvm (default "qemu-kvm")
  -r int
    	runs x times then terminates. default -1 (runs forever) (default -1)
  -s int
    	sleep n seconds between runs. default 1s (default 1)
  -uuid
    	show uuid  (default: false)
  -version
    	show version
```

Exemplary output
```
vmname CpuCS CpuVM CpuPM CpuST CpuIO ram-used ram-total
vm1    2     5%    5%    0%    1%    1024MB   1024MB
vm2    4     15%   15%   0%    1%    1024MB   1024MB
```

## Setup developer workspace or compile kvmtop

```
mkdir kvmtop && cd kvmtop
go get "github.com/cha87de/kvmtop"
go get "github.com/rthornton128/goncurses"
export GOPATH=`pwd`
go install github.com/cha87de/kvmtop
```

The `go install` will produce a binary located at ./bin/kvmtop


# Export Schedstat from Kernel

In CENTOS7 Kernel, the schedstats are not exported any more [1][2].
One way of installing a kernel, which exports the necessary metrics is to use the ELRepo kernel-ml:

```
# import key
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# install repository
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
# install most current kernel-ml
yum --enablerepo=elrepo-kernel install kernel-ml
```

Reboot into newly installed kernel. Then remove the old kernels!

```
yum list kernel*
yum erase kernel
```

Take care with diskless nodes. To boot into new kernel, 
the file pxelinux.cfg/default has to be changed on the storage node.


[1] https://bugzilla.redhat.com/show_bug.cgi?id=1013225
[2] https://www.centos.org/forums/viewtopic.php?f=48&t=54049

