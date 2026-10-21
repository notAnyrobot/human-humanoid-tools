# Astro P2 in this fork

The `develop` branch keeps HHTools code and the canonical Astro robot bundle together.
The fork's `main` branch mirrors upstream; keep custom commits on `develop`.
Edit and commit `configs/robots/astro_p2_29dof/` directly; no separate asset
repository, generation step, or robot-library environment override is needed.

```text
configs/robots/astro_p2_29dof/
├── robot.yaml
├── astro_p2_29dof.urdf
├── meshes/
└── SOURCE.json
```

The preset has 29 active joints. Head yaw (`head_joint`) is fixed at zero,
while the head geometry, collision, mass and inertia are retained. The joint
order is pinned in `robot.yaml`; no calibration is bundled.

## Use on each device

```bash
git clone --branch develop https://github.com/notAnyrobot/human-humanoid-tools.git
cd human-humanoid-tools
unset HHTOOLS_ROBOT_DIR HHTOOLS_ROBOT_PATH
uv sync --locked --extra all
uv run python scripts/install_builtin_robots.py
cp -a configs/robots/astro_p2_29dof ~/.config/hhtools/robots/
uv run hhtools robot validate astro_p2_29dof
uv run hhtools web
```

The commands above use the default Linux user Robot Library. Remove old
robot-library overrides before installing. The copy command is for a fresh
installation; back up an existing Astro directory before reinstalling it.

All seven runtime bundles live together:

```text
~/.config/hhtools/robots/
├── astro_p2_29dof/
├── g1_29dof/
├── roboto_origin/
├── agibot_x2_ultra/
├── asimov_1/
├── fourier_gr2/
└── berkeley_humanoid_lite/
```

The installed Astro copy takes precedence over the same-named workspace preset.
MCP discovers it under `robot-library`. The fork keeps the canonical copy under
`configs/robots/astro_p2_29dof` so it can be installed on another device. No robot
library environment override is needed.

For MCP, use this checkout's `.codex/config.toml` and reconnect after changing
the checkout or launch configuration. Ensure the MCP working directory is
this checkout and its `--source` points to an existing motion directory.
The stdio server owns its runtime; its WebUI URL is only a fallback link.

## Synchronize

Commit robot YAML, URDF, meshes and reviewed calibration changes on `develop`
and pull that branch on the other devices. If you edit the installed Astro copy,
copy the reviewed changes back to `configs/robots/astro_p2_29dof` before committing.
After pulling changes, update each device's installed copy, preserving any local
calibration you still need. Keep datasets, outputs and caches outside Git.
Record the repository revision and dataset version for each run.

HHTools can save calibration under the user library, especially for Agent
calibration overlays. Such files are not synchronized by this fork until they
are explicitly reviewed and added to the Astro bundle. Check for higher-priority
local calibration overrides when comparing devices. Register and inspect a new
bundle after changing its contents, then perform a fresh preflight.

The loader may normalize URDF XML in place; review diffs before committing.
Calibrate separately for each input reference family and inspect short clips
before processing a full dataset.

## Official robots and upstream updates

Official robots remain available in the default user library:

```bash
uv run python scripts/install_builtin_robots.py
```

That command downloads pinned upstream assets; it does not add them to this
fork. Use `--only g1_29dof` to install just G1.

Keep `origin` pointing to this fork and `upstream` pointing to Roboparty:

```bash
git remote add upstream https://github.com/Roboparty/human-humanoid-tools.git
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
git switch develop
git merge main
git push origin develop
```

Add `upstream` only once per clone. Keep `main` free of custom commits. Resolve
any conflicts on `develop` before pushing.
