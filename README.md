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

> Follwing list of arguments was heavily influenced by discussion in
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
