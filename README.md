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

### How to prepare new release of Dasharo (coreboot+SeaBIOS) for PC Engines?

#### Applying patches

Start with standard procedure described above, if `guilt push -a` does not
apply given patch cleanly force patch application (`guilt push -f`) and fix
manually auto-generated `.rej` files may be helpful in resolving patch
application issues. After resolving issues for given patch use `git add` to
stage required changes and `guilt refresh` to update patch, so it reflects
cleanly applicable change.

At this point you may need to check if code still compiles at this point, that
why [pce-fw-builder update](#pce-fw-builder-update) may be useful.

#### pce-fw-builder update

To understood what changes may be needed to
[pce-fw-builder](https://github.com/pcengines/pce-fw-builder) when updating to
new coreboot version, please check [this
MR](https://github.com/pcengines/pce-fw-builder/pull/66).

## Background

### Motivation

`dasharo-pq` repository was started as proof of concept to resolve issues
mentioned in [#310](https://github.com/Dasharo/dasharo-issues/issues/310)
related to currently used strategies in managing the downstream distribution of
coreboot firmware. The main points of concern include:

1. **Branching Model Complexity**: The current approach of maintaining separate
   per-platform branches is becoming increasingly burdensome as the number of
   supported platforms grows.
1. **Synchronization Issues**: Ensuring that features and fixes are
   synchronized across multiple branches often requires additional effort and
   can lead to inconsistencies.
1. **Single branch problem**: Despite switching to single branch for releases
   (`dasharo` branch) rebasing will be more and more painful as number of
   changes will grow.
1. **Upstream Merging Difficulties**: Incorporating changes from the upstream
   coreboot repository into the downstream forks is becoming progressively
   difficult, with a high risk of missing important updates or fixes. Complexity
   may grow even more if we will face changes which cannot be upstreamed.

#### The Role of Patch Management

Switching to a patch management system can significantly alleviate these issues
by providing a structured and systematic approach to managing changes.

#### Applying Lessons from Other Projects

Structured patch management is used by maintainers of big projects like Debian,
Linux, Xen and smaller like TrenchBoot. Patch management seem to be foundation
for downstream distribution maintenance and it was recommended as a potential
solution for issues mentioned in
[#310](https://github.com/Dasharo/dasharo-issues/issues/310).

## FAQ

### Why Track Patches in Git Instead of Just Using Branches?

**Disclaimer**: Our current understanding of patch management systems, such as
guilt, is still at a low level. Therefore, the following benefits are more of a
hypothesis, to be proven and a basis for discussion, but were built on top of
logical consequences of using patch based systems. We may want to improve the
following arguments in the future.

#### **Handling Upstream Requests for Patch Changes**

**Scenario:** An upstream project requests changes to a patch, that was/is/will
be included in release, before accepting it. Managing this scenario downstream
using branches can be complex and error-prone.

**Benefit of Patch Management:**

- **Ease of Modification:** Patch management tools like Guilt make it
  straightforward to modify individual patches without disrupting other changes.
  You can edit the specific patch, refresh it, and reapply the patch queue.
- **Minimal Disruption:** This approach avoids the complications of merging or
  rebasing entire branches, which can introduce conflicts and disrupt other
  ongoing work.

#### **Creating Multiple Releases with Different Patch Combinations**

**Scenario:** A maintainer needs to create multiple different releases using
various combinations of patches. Some features may be included or excluded
based on specific requirements.

**Benefit of Patch Management:**

- **Selective Application:** Patches can be selectively applied or excluded
  based on the requirements of each release. This flexibility allows maintainers
  to create customized versions without duplicating the entire codebase. Patch
  management system help to track what was applied where.
- **Reduced Overhead:** By managing features as patches, maintainers can
  include or exclude features without maintaining separate branches for each
  combination, reducing overhead and complexity.

#### **Managing Legacy and Customer-Specific Patches**

**Scenario:** Some patches may be legacy or specific to certain customers who
do not require frequent updates.

**Benefit of Patch Management:**

- **Legacy Support:** Patches can be maintained and applied independently,
  allowing legacy or customer-specific modifications to be preserved without
  merging them into the main branch.
- **Customization:** This approach enables maintainers to provide customized
  builds for different customers, each with its own set of patches.

#### **Reproducible Builds**

**Scenario:** Ensuring that specific builds can be reproduced consistently is
crucial for debugging, testing, and compliance.

**Benefit of Patch Management:**

- **Consistency:** Patch management ensures that the same set of patches can be
  applied in the same order, resulting in identical builds every time.
- **Auditability:** Detailed patch logs provide a clear history of what changes
  were made, when, and by whom, making it easier to audit and reproduce specific
  builds.
