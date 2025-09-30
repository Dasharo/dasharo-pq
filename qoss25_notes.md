* use patch file names instead of -n 16
* it is better to do git format-patch without summary, there can be alias for that
	- format-patch -kp --no-signature
	- tell git format-patch to not name file names with numbers (-N?)
* ensure that short sha is use 12 characters core.abrev=12
* while :; do guilt push && guilt refresh; done
- with the above it would cleanup patchqueue to remove summaries and git signature because it will cause redundant changes
- patchqueue is optimized for mataining the delta with upstream, not to prepare
for upstream, although it can be helpful
- clean patchqueue does not have messy diffs of diffs (patches of patches)
- every commit in patchqueue should cleanly push all patches
	- so guilt push -a is kind of build process for patchqueue
- there should be mechanical changes to the patches, Andrew want to do
- emacs has magit which helps, nvim has https://github.com/NeogitOrg/neogit
- every change should follow build and make sure build is still reproducbile
- build system integration should be one patch
- use local .gitignore files, if you add new directory place gitignore there

