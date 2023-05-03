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

Renode recently got 64-bit ARM support, but unfortunately I haven't been able to get it
working yet, here is the log:

```
10:49:42.8683 [INFO] Including script: /home/ivanv/ts/sel4_renode/cortex-a53-sel4test.resc
10:49:42.8780 [INFO] System bus created.
10:49:43.5010 [INFO] sysbus: Loading segment of 5124280 bytes length at 0x40A30000.
10:49:43.5193 [INFO] cpu: Setting PC value to 0x40A3D000.
10:49:43.5383 [INFO] ARM Cortex-A53: Machine started.
10:49:43.6744 [ERROR] uart0: DATA register cannot be written to; UARTEN is disabled! (616)o; UARTEN is disabled! (550)
10:49:43.6760 [WARNING] sysbus: [cpu: 0xFFFFFF8040000000] ReadByte from non existing peripheral at 0xFFFFFF8040000000.
10:49:43.6761 [ERROR] cpu: CPU abort [PC=0xFFFFFF8040000000]: Trying to execute code outside RAM or ROM at 0xFFFFFF8040000000.
```

There is a [GitHub issue](https://github.com/renode/renode/issues/453) on the Renode repository
about this issue already, hopefully it will be resolved.
