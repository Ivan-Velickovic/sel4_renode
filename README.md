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

For AArch64 it looks like Renode can successfully start and run sel4test, the main problem
right now is that the simulation is *extremely* slow.
