## "Live patching" of `actions/cache` to let it cache even when a job fails

GitHub's action `actions/cache` only saves data in the cache, if the job succeeds.  There has been much interest in letting `actions/cache` save data in the cache even if the job fails, see [its issue \#92](https://github.com/actions/cache/issues/92).  This action `Olf0/cache-always` is a direct replacement for `actions/cache` versions 1, 2 and 3.<br />
<sub>Note that GitHub has provided [an alternative solution](https://github.com/actions/cache/discussions/1020) by releasing two new actions [`actions/cache/save`](https://github.com/actions/cache/tree/main/save) and [`actions/cache/restore`](https://github.com/actions/cache/tree/main/restore) in December 2022, but this requires significant changes to extant workflows which use GitHub's classic [`actions/cache`](https://github.com/actions/cache), plus all three actions continue to be maintained by GitHub (so it was indicated in [this "Conclusion"](https://github.com/actions/cache/discussions/1020#discussion-4635717)).  Still [this extension of the original "action `cache`](https://github.com/MartijnHols/actions-cache) by [Martijn Hols](https://github.com/MartijnHols), which is structurally analog to `actions/cache/save` and `actions/cache/restore`, continues to provide a larger feature set!</sub>

Rather than forking and adapting GitHub's `actions/cache`, which creates a maintenance burden, this repository demonstrates "live patching" of the original `actions/cache` on every job run to accomplish this goal, see [.github//actions/cache-always/action.yml](https://github.com/Olf0/cache-always/blob/main/.github/actions/cache-always/action.yml).  It does …

- … [check out `actions/cache` to `~/.github/.tmp/actions/cache-always`](https://github.com/Olf0/cache-always/blob/main/action.yml#L27-L32)
- … [patch `post-if: success()` to `post-if: ${{ success() || failure() }}`](https://github.com/Olf0/cache-always/blob/main/action.yml#L34-L35) to let the patched action cache data, even if the job fails, but not when it is cancelled.
- … transparently map the "live patched" action to the patching action `Olf0/cache-always` by passing through all parameters, so the latter can be called.

Ultimately one simply substitutes all occurrences of `actions/cache@vX` in own workflows with `Olf0/cache-always@vX` (`X` being `1`, `2` or `3`).<br />

For an example of a workflow using the "live patched" `actions/cache` in `.github/actions/cache-always` see [.github/workflows/demo-cache-always.yml](https://github.com/Olf0/cache-always/blob/main/.github/workflows/demo-cache-always.yml).

P.S.: Special thanks to [DrJume](https://github.com/DrJume) for some [good suggestions](https://github.com/actions/cache/issues/92#issuecomment-1263067512) which have been used as a basis for further improvements.
