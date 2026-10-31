# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Sphinx (reStructuredText) source for https://docs.platformio.org/. There is no application code and no test suite; the only real "check" is a clean Sphinx build. The default/working branch is `develop`.

## Building

```
make html          # output in _build/html   (Windows: make.bat html)
make linkcheck     # check external links (conf.py sets linkcheck_timeout=10, anchors not checked)
sphinx-build -b html . _build/html -W   # treat warnings as errors when validating a change
```

Required Python packages (from `conf.py` extensions): `sphinx`, `sphinx_rtd_theme`, `sphinx-notfound-page`, `sphinx-copybutton`, `sphinxcontrib-googleanalytics`, **and `platformio` itself** — `conf.py` does `import platformio` to derive `version`/`release` and whether the build is "latest" (dev version) or stable. `conf.py` also adds the parent directory to `sys.path`, because this repo is normally checked out as the `docs/` submodule of `platformio-core`, so the in-tree `platformio` package is picked up from there.

## Structure and conventions

- `index.rst` is the master toctree. Top-level folders map to doc sections (`core/` CLI + user guide, `projectconf/` `platformio.ini` reference, `advanced/`, `integration/` IDE/CI, `plus/`, `librarymanager/`, `manifests/`, `scripting/`, `tutorials/`, `faq/`, `home/`).
- **Generated content:** `platforms/*.rst`, `frameworks/*.rst` and everything in `boards/` are generated from dev-platform/board manifests (commits titled "Sync dev-platforms"/"Sync with dev platforms") by tooling in platformio-core, not hand-edited. Hand-written additions for a platform/framework go into the matching `*_extra.rst` file, which the generated page pulls in via `.. include:: <name>_extra.rst`. `*_extra.rst` files are excluded from standalone build (`exclude_patterns` in `conf.py`). Hand-written exceptions in `platforms/` include `creating_board.rst`, `creating_platform.rst`, `custom_platform_and_board.rst`.
- Every `.rst` starts with the Apache-2.0 license comment block followed by a `.. _label:` anchor; cross-reference with `:ref:` using these labels (e.g. `cmd_run`, `projectconf_env_platform`, `platform_atmelavr`) rather than file paths.
- CLI command pages (`core/userguide/**/cmd_*.rst`) follow a fixed layout: `.. contents::`, Usage (`code-block:: bash`), Description, Options using `.. program::` + `.. option::`, Examples. Mark new behavior with `.. versionadded:: X.Y.Z`.
- `projectconf/sections/env/options/<group>/` holds one page per `platformio.ini` option.

## Moving or removing pages

When a page is renamed/moved/deleted, add a line to `redirects.txt` in the form `/old/path.html -> /new/path.html` (both sides must start with `/` and end in `.html`). The local extension `_ext/redirects.py` generates HTML redirect stubs from this file after the HTML build.

## Theme

`_templates/` overrides `sphinx_rtd_theme` layout (navbar, footer, versions selector). `html_context` in `conf.py` drives the "Edit on GitHub" link (`platformio/platformio-docs`, branch `develop`) and the latest/stable version switcher.
