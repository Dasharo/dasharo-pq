# Dasharo Patchqueue repository

For any new features, please work with the Dasharo project by contacting
[developers](https://docs.dasharo.com/#commercial-inquiries) or
[community](https://docs.dasharo.com/#community).

Refer to [Dasharo
Contribution](https://docs.dasharo.com/newcomers/#dasharo-contribution) for
information on contributing to the Dasharo project.

## Usage

Following procedure was created while testing PC Engines v4.19.0.1. It should
be generalized after gaining better understanding of the process.

Clone upstream coreboot, create a branch called patchqueue:

```bash
git clone https://review.coreboot.org/coreboot
```

```bash
cd coreboot
```

Checkout branch on tag on which you would like to apply patchqueue:

```bash
git checkout 4.19 -b patchqueue
```

Clone this repo:

```bash
git clone -b pcengines-seabios-25.05.00.01 git@github.com:Dasharo/dasharo-pq.git .git/patches
```

Point the `patchqueue` Branch at the Patches Inside `.git/patches`:

```bash
ln -s patches .git/patches/patchqueue
```

```bash
touch .git/patches/patchqueue/status
```

Initialize guilt:

```bash
guilt init
```

Push All Patches Using Guilt:

```bash
guilt push -a
```

This should give output similar to:

```bash
Applying patch..0001-configs-remove-apu1-and-add-apu2-4.patch
Patch applied.
Applying patch..0002-payloads-seabios-use-PC-Engines-repository.patch
(...)
Applying patch..0080-src-southbridge-amd-pi-hudson-Makefile.inc-fix-AMDFW.patch
Patch applied.
```

This is how git tree should look like:

````bash
* 4ea630bd33cb (HEAD -> guilt/patchqueue) From 3812d71fa7cbb096b4af310ed4910ecb8b723fd7 Mon Sep 17 00:00:00 2001 [PATCH 80/80] src/southbridge/amd/pi/hudson/Makefile.inc: fix AMDFW
* 6bc070131370 From e231b58275e9f177abd13cf60fafa29b83c6349d Mon Sep 17 00:00:00 2001 [PATCH 79/80] mainboard/pcengines: make HUDSON_LEGACY_FREE n by
* 0dd87c9fb3c3 From a80c0e940afee2fa80f6ff9e24292cd55e016104 Mon Sep 17 00:00:00 2001 [PATCH 78/80] mainboard/pcengines: fix sign-of-life coreboot build
* 28129f80b8a7 From d55f700035eba51312308523c150e469d667168e Mon Sep 17 00:00:00 2001 [PATCH 77/80] mb/pcengines/apu2: add variant apu7
* 4f463183808a From 70997c8bb05c22d483c61be2bea4dbcc67fd2a7b Mon Sep 17 00:00:00 2001 [PATCH 76/80] payloads/external/SeaBIOS: add bootorder in FMAP option
* 2586783ba027 From 17b503d79a81e9cda8e21966fcea381f4f9720b3 Mon Sep 17 00:00:00 2001 [PATCH 75/80] src/northbridge/amd/pi/00730F01/northbridge.c: enable
* 158374799a64 From 299ef598aca1512678db42c30e7cad0c83e5d43d Mon Sep 17 00:00:00 2001 [PATCH 74/80] nb/amd: enable ProcessorScopeInSb for fam14 and
* 1f7a95467a77 From 12594a0e7edb0fd6ea073cae0a907bfb264d6bca Mon Sep 17 00:00:00 2001 [PATCH 73/80] pcengines/apu2: enable EHCI controller by default
* 251ea31b8d6e From 8a376acfe8d8c8baa1ce885c3882993cca2b3da8 Mon Sep 17 00:00:00 2001 [PATCH 72/80] amdblocks/psp_efs.h: allow for PI 00730F01 platforms
* 3ed4083d0bb2 From 76a167dd928f766722ecd4c47ceb08f8a5508557 Mon Sep 17 00:00:00 2001 [PATCH 71/80] mainboard/pcengines: avoid ASSERT on the stack
```

Check if patchqueue was applied:

```bash
guilt applied
````

## Development

### How to handle bug in patches ?

- pop patches using `guilt pop` until you will have broken one on top of stack,
- modify files,
- `guilt refresh` - this will store changes in required patch
- `guilt push -a` - to make sure all patches on top apply without problems
- commit changes to this repository

Read `man guilt-pop` for efficient popping.

### How to avoid coreboot version issues?

coreboot version is built using output from `git describe`. After applying out
changes we will have `git describe` output similar to `4.19-80-g3812d71fa7`. To
avoid such issue before building you have to add annotated tag on top of
patchqueue:

```bash
git tag -a v4.19.0.1 -m "This is just testing if patchque really works"
```

After that operation produced coreboot binary will have `v4.19.0.1` as version.
