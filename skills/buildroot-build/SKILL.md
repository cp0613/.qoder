---
name: buildroot-build
description: Build buildroot config images for RISC-V targets. Use when the user wants to compile, build, or make a buildroot defconfig image, or mentions building a specific config like qemu_xuantie_defconfig.
---

# Buildroot Build

Build buildroot defconfig images from the project's `configs/` directory.

## Project Info

- **Project root**: The repository root (where this `.qoder/` directory lives)
- **Configs directory**: `configs/` (files ending with `_defconfig`)
- **Build command**: `make CONF=<config_name> [target]` (run from project root)

## Workflow

### Step 1: Determine the config

If the user specifies a config name (with or without `_defconfig` suffix), use it directly. Otherwise, list available configs and ask the user to choose:

```bash
ls configs/
```

Use the `AskUserQuestion` tool to let the user select one or more configs. Present configs as options.

### Step 2: Determine the build target (optional)

If the user specifies a build target, append it to the make command. Common targets:

| Target | Description |
|--------|-------------|
| *(empty)* | Full build (default) |
| `opensbi` | Build OpenSBI firmware only |
| `linux-menuconfig` | Configure Linux kernel |
| `linux-rebuild` | Rebuild Linux kernel |
| `linux-dirclean` | Clean Linux kernel build |
| `uboot-rebuild` | Rebuild U-Boot |
| `uboot-dirclean` | Clean U-Boot build |
| `<pkg>-rebuild` | Rebuild a specific package |
| `<pkg>-dirclean` | Clean a specific package |

If the user doesn't specify a target, do a full build (no target suffix).

### Step 3: Run the build

Run the build from the project root directory using a **background bash** so the user can see real-time output:

```bash
make CONF=<config_name> [target]
```

Use `run_in_background: true` for long-running full builds. Use `BashOutput` to monitor progress.

For quick targets like `linux-menuconfig`, run in foreground.

### Step 4: Report results

After the build finishes, report whether it succeeded or failed. If it failed, show the relevant error output.

## Building multiple configs

If the user wants to build multiple configs, build them **sequentially**. Track progress using `TodoWrite` with one todo per config.

## Examples

```bash
# Full build
make CONF=qemu_xuantie_defconfig

# Build opensbi only
make CONF=qemu_xuantie_defconfig opensbi

# Configure Linux kernel
make CONF=qemu_xuantie_defconfig linux-menuconfig
```
