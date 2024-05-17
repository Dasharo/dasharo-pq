# Dasharo Patchqueue repository

For any new features, please work with the Dasharo project by contacting
[developers](https://docs.dasharo.com/#commercial-inquiries) or
[community](https://docs.dasharo.com/#community).

Refer to [Dasharo
Contribution](https://docs.dasharo.com/newcomers/#dasharo-contribution) for
information on contributing to the Dasharo project.

This repo a guilt patchqueue. To set up for use with guilt:

Clone upstream coreboot, create a branch called patchqueue:

```bash
git clone https://review.coreboot.org/coreboot
```

```bash
cd coreboot
```

Checkout branch on tag on which you would like to apply patchqueue:

```bash
git checkout 4.19 -b my-branch
```

Initialize guilt:

```bash
guilt init
```

Leave coreboot directory:

```bash
cd ..
```

Clone this repo:

```bash
git clone git@github.com:Dasharo/dasharo-pq.git
```

Enter dasharo-pq patches directory

```bash
cd dasharo-pq/patches
```

```bash
touch .git/patches/my-branch/status
```

Push the patchqueue

```bash
guilt push -a
```

As development progresses, the base of the patchqueue moves. Refer to
dasharo.spec for the appropriate base to use. To update the base when it moves:

Pop the patches

```bash
guilt pop -a
```

Move the patchqueue branch to the new base

```bash
git reset --hard $NEW_BASE
```

Push the patchqueue at its new base

```bash
guilt push -a
```

Refer to the specfile for dependencies and build instructions.

## Getting started with new repository
