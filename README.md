# mesa-turnip

GameNative's Turnip tree. Turnip is Mesa's Vulkan driver for Qualcomm Adreno GPUs (`src/freedreno/vulkan`). This repository follows upstream Mesa `main` and keeps the GameNative patches on top. Each pull request builds an AdrenoTools zip that you can import into GameNative.

The upstream Mesa README is [README.rst](README.rst).

## Branches

- `turnip` (default): upstream Mesa plus our patches. All work goes here through pull requests.
- `upstream-main`: a mirror of `https://gitlab.freedesktop.org/mesa/mesa.git` `main`. The sync workflow force-pushes it. Never commit to it.

## Workflows

- `.github/workflows/turnip-pr.yml` runs on each pull request to `turnip`, and you can also start it from the Actions tab. It calls the reusable build in [GameNative/turnip-ci](https://github.com/GameNative/turnip-ci) (`build.yml` with `use_checkout: true`) and builds the PR's tree. The zip is uploaded as the artifact `turnip-pr-<number>-<short sha>`. The driver shows in GameNative as `pr-<number>`.
turnip-ci is private, and the `GITHUB_TOKEN` of this repository cannot read it. Thus the repository secret `TURNIP_CI_TOKEN` must contain a token that can read `GameNative/turnip-ci` (fine-grained, Contents: read).

- `.github/workflows/upstream-sync.yml` runs every day and from the Actions tab. It mirrors upstream `main` to `upstream-main`. If `upstream-main` has commits that are not in `turnip`, it opens the pull request "Sync with upstream Mesa main" (or updates the body of the open one). Merge that pull request with a merge commit, not squash or rebase.

## Adding a patch

1. Make a branch from `turnip`: `git checkout -b fix-something origin/turnip`.
2. Commit the change and open a pull request to `turnip`.
3. When "Turnip PR build" is complete, download the zip: `gh run download -R GameNative/mesa-turnip <run-id>`, or use the artifact on the run page. The zip is in a second zip; extract only the outer one.
4. Copy the zip to the device. In GameNative, go to **Settings > Emulation > Driver Manager > Import ZIP from device**. Then select the driver `pr-<number>` in the game's or container's graphics driver settings.
5. Merge the pull request when the driver works on the target devices.

`variants/` records the community build matrix (which trees and patches each GPU family needs) as data for later automation.

This line is a CI smoke test.
