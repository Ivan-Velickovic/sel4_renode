# Simulating seL4 with Renode

*Note, this repository is a work-in-progress*

The purpose of this repository is to give examples of using the Renode
simulator with the seL4 microkernel. This is primarily done by simulating the
seL4 test suite, seL4test.

The hope is the work put into these examples will help people using seL4 evaluate
whether Renode is appropriate for them.

Note that if you want to run these examples I recommend that you [build Renode from
source](https://renode.readthedocs.io/en/latest/advanced/building_from_sources.html).

## RISC-V

### Caveats

Test `PAGEFAULT0005` does not work, unclear whether the test is wrong or Renode is at
this moment. I need to look into it!

### Platforms

#### QEMU RISC-V virt

seL4 currently supports the virtual RISC-V platform that QEMU provides, we can emulate
this system using Renode as well:

```sh
renode -e "start @qemu-riscv-virt-sel4test.resc" --console
```

#### SiFive HiFive Unleashed

```sh
renode -e "start @hifive-unleashed-sel4test.resc" --console
```

Looks like hart switching does not work on Renode (at least from what I can tell) so
when the OpenSBI booting hart ID does not match seL4's `CONFIG_FIRST_HART_ID`, the
whole system halts without getting past the hart switching to seL4 and seL4test
unfortunately.

#### Pine64 Star64

TODO

## ARM

### QEMU ARM virt

```sh
renode -e "start @cortex-a53-sel4test.resc" --console
```

Renode recently got 64-bit ARM support, but unfortunately I haven't been able to get it
working yet, here is the UART output:

```
Not in hyp mode, cannot reset CNTVOFF_EL2
ELF-loader started on CPU: ARM Ltd. Cortex-A53 r0p4
  paddr=[40a3d000..40f130b7]
No DTB passed in from boot loader.
Looking for DTB in CPIO archive...found at 40b6aee8.
Loaded DTB from 40b6aee8.
   paddr=[4023f000..40240fff]
ELF-loading image 'kernel' to 40000000
  paddr=[40000000..4023efff]
  vaddr=[ffffff8040000000..ffffff804023efff]
  virt_entry=ffffff8040000000
ELF-loading image 'sel4test-driver' to 40241000
  paddr=[40241000..40647fff]
  vaddr=[400000..806fff]
  virt_entry=40e700
Enabling MMU and paging
Jumping to kernel-image entry point...
```

And here is the log from Renode:
```
16:58:57.8464 [WARNING] sysbus: [cpu: 0xFFFFFF8040000000] ReadByte from non existing peripheral at 0xFFFFFF8040000000.
16:58:57.8464 [ERROR] cpu: CPU abort [PC=0xFFFFFF8040000000]: Trying to execute code outside RAM or ROM at 0xFFFFFF8040000000.
```

From this it seems like there is something going wrong when configuring the MMU and jumping to the
kernel as `0xFFFFFF8040000000` represents the virtual address of the kernel entry point.
