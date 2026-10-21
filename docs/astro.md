# Astro P2 in this fork

This fork keeps HHTools code and the canonical Astro robot bundle together.
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
git clone https://github.com/notAnyrobot/human-humanoid-tools.git
cd human-humanoid-tools
uv sync --locked --extra all
uv run hhtools robot validate astro_p2_29dof
uv run hhtools web
```

If this shell previously sourced the Menagerie X environment script, remove
those overrides first:

```bash
unset HHTOOLS_ROBOT_DIR HHTOOLS_ROBOT_PATH
```

The checkout's `configs/robots` directory is discovered automatically by
WebUI/CLI and by MCP as `workspace-robots`. The default user Robot Library
remains available for official HHTools robots. Do not keep another same-named
Astro preset in the user library or additional discovery roots.

For MCP, use this checkout's `.codex/config.toml` and reconnect after changing
the checkout or launch configuration. Ensure the MCP working directory is
this checkout and its `--source` points to an existing motion directory.
The stdio server owns its runtime; its WebUI URL is only a fallback link.

## Synchronize

Commit robot YAML, URDF, meshes and reviewed calibration changes to this fork
and pull them on the other devices. Keep datasets, outputs and caches outside
Git. Record the repository revision and dataset version for each run.

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
git merge upstream/main
git push origin main
```

Add `upstream` only once per clone. Resolve any merge conflicts before pushing.
