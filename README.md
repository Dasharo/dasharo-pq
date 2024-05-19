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

Check if patchqueue was applied:

```bash
guilt applied
```

## Getting started with new repository

## Background

### Motivation

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
