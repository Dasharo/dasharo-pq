# Dasharo Patchqueue repository

For any new features, please work with the Dasharo project by contacting
[developers](https://docs.dasharo.com/#commercial-inquiries) or
[community](https://docs.dasharo.com/#community).

Refer to [Dasharo
Contribution](https://docs.dasharo.com/newcomers/#dasharo-contribution) for
information on contributing to the Dasharo project.

## Usage

Following procedure was created while testing PC Engines v24.05.00.01 and
retested while releasing v24.08.00.01. It should be generalized after gaining
better understanding of the Dasharo Patchqueue Initiative.

### Prepare code base

Clone upstream coreboot, create a branch called patchqueue:

```bash
git clone https://review.coreboot.org/coreboot
```

```bash
cd coreboot
```

Checkout branch on tag on which you would like to apply patchqueue:

```bash
git checkout 24.08 -b patchqueue
```

Clone this repo:

```bash
git clone https://github.com/Dasharo/dasharo-pq.git .git/patches
```

Go to `.git/patches` and checkout correct patchqueue version:

```bash
cd .git/patches
```

In this case re plan to build Dasharo (coreboot+SeaBIOS) v24.08.00.01 for PC
Engines:

```bash
git checkout v24.08.00.01
```

Get back to coreboot main directory:

```bash
cd -
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

```bash
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
```

Tag repository, otherwise hash of the build will not match since coreboot use
tag during build process:

```sh
git tag -a v24.08.00.01 -m "Some annotation which conten really doesn't matter"
```

**NOTE:** It has to be annotated tag, coreboot does not pick others.

## Release process notes

After applying all patches binaries for all platforms can be build:

```sh
./build.sh seabios_apu2 && ./build.sh seabios_apu3 && ./build.sh seabios_apu4 \
&& ./build.sh seabios_apu6
```

## Development

### How to handle bug in patches?

- Pop patches using `guilt pop` until you will have broken one on top of stack,
- Modify files,
- `guilt refresh` - this will store changes in required patch
- `guilt push -a` - to make sure all patches on top apply without problems
- Commit changes to this repository

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

### How to prepare new release of Dasharo (coreboot+SeaBIOS) for PC Engines?

#### Applying patches

Start with standard procedure described above, if `guilt push -a` does not
apply given patch cleanly force patch application (`guilt push -f`) and fix
manually auto-generated `.rej` files may be helpful in resolving patch
application issues. After resolving issues for given patch use `git add` to
stage required changes and `guilt refresh` to update patch, so it reflects
cleanly applicable change.

## Background

The `dasharo-pq` repository was created as a proof of concept to address issues
mentioned in [#310](https://github.com/Dasharo/dasharo-issues/issues/310)
regarding the current strategies for managing the downstream distribution of
coreboot firmware.

It is important to acknowledge that our understanding of patch management
systems, such as `guilt`, is still limited. Therefore, the following benefits
are more of a hypothesis to be tested and a starting point for discussion, but
are based on logical consequences of using patch-based systems. These arguments
may need improvement in the future.

### Motivation

1. **Branching Model Complexity**: The current approach of maintaining separate
   per-platform branches is becoming increasingly burdensome as the number of
   supported platforms grows.
1. **Synchronization Issues**: Ensuring that features and fixes are
   synchronized across multiple branches often requires additional effort and
   can lead to inconsistencies.
1. **Mono-branch Problem**: Despite switching to a mono-branch for releases
   (`dasharo` branch), rebasing will become increasingly painful as the number
   of changes grows. With a mono-branch, you have accumulated bug fixes, features,
   and configurations for multiple platforms. While merging this branch with
   upstream periodically is an option, it loses the separation between upstream
   and local changes. Rebasing the branch increases the risk of errors, and
   force-pushing breaks collaboration.
1. **Upstream Merging Difficulties**: Incorporating changes from the upstream
   coreboot repository into downstream forks is becoming progressively
   difficult, with a high risk of missing important updates or fixes. Complexity
   may grow even more if changes cannot be upstreamed.

#### The Role of Patch Management

Switching to a patch management system can significantly alleviate these issues
by providing a structured and systematic approach to managing changes.

#### Applying Lessons from Other Projects

Structured patch management is used by maintainers of large projects like
Debian, Linux, Qubes OS and XenServer, as well as smaller projects like
TrenchBoot. Patch management appears to be foundational for downstream
distribution maintenance and was recommended as a potential solution for issues
mentioned in [#310](https://github.com/Dasharo/dasharo-issues/issues/310).

## FAQ

### Why Track Patches in Git Instead of Just Using Branches?

> Following list of arguments was heavily influenced by discussion in
> [#5](https://github.com/Dasharo/dasharo-pq/pull/5). Kudos to:
> [@krystian-hebel](https://github.com/krystian-hebel),
> [@andyhhp](https://github.com/andyhhp),
> [@SergiiDmytruk](https://github.com/SergiiDmytruk).

Each method (mono-branch and patchqueue) discussed has its own advantages and
disadvantages, and the preference may vary depending on the team's familiarity
and comfort with each approach.

1. **Version-Controlled Patch Representation:** Using a patchqueue (e.g., with
   `guilt`), the canonical representation of the mono-branch is in patch form,
   which is version-controlled. This avoids rewriting history and allows large
   "rebases" to be done in steps, each verified for correctness.
1. **Intermediate Solution:** While a properly done rebase keeps history clean,
   not all developers are proficient enough to avoid creating a mess. A patches
   repository can serve as a middle ground until reeducation is complete.
1. **Enforcing Best Practices and Early Upstreaming:** Forcing developers to
   use patches from the beginning ensures adherence to best practices and
   encourages upstreaming more code early. This reduces the downstream maintenance
   burden and the risk of creating a difficult-to-manage history. Once code is
   merged upstream, both upstream and downstream can address fixes in parallel,
   minimizing duplicated efforts and conflict resolution time.
1. **Effective Branch Management:** Grouping patches similarly to how Qubes
   does it (e.g., using a `guilt` series file) can help manage multiple
   branches effectively and systematically.
1. **Simplified Build Testing:** Testing if a set of patches builds with the
   current coreboot can be simpler and less intimidating than rebasing a
   branch. It is also easier to automate, providing immediate feedback on upstream
   changes that might fix the same issues or potentially break existing work.
1. **Balanced Approach:** This proposal aims to balance maintaining history
   integrity and encouraging upstream contributions.
1. **Reduced Overall Effort:** Although patchqueues require regular
   maintenance, they can ultimately reduce the overall effort needed to manage
   changes.

By trying both approaches simultaneously, ideally in an automated manner, we
can determine the best method without fully committing to one approach
initially. This flexibility allows us to evaluate the effectiveness and
practicality of using patch queues in our development workflow. Testing this
approach in a hackathon or through parallel workflows may help determine its
viability.
