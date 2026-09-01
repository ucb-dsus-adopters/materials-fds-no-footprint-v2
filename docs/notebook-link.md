# Running these materials on Notebook.link

[Notebook.link](https://notebook.link) runs these notebooks in the browser
through JupyterLite. No install, no server, no DataHub account.

- Latest commit on the default branch:
  <https://notebook.link/github/ucb-dsus-adopters/materials-fds-no-footprint-v2/>
- Short alias pointing at `main`:
  <https://notebook.link/@caa8ee2f-1d28-45d1-844d-b247720c27d9/data8>

## Why the materials are split across two repos

Notebook.link refuses a repository over 50 MB, and the datasets blow past that
on their own. So the data lives in
[materials-fds-assets-v2](https://github.com/ucb-dsus-adopters/materials-fds-assets-v2),
published through GitHub Pages, and the notebooks fetch it over HTTP:

```python
farmers_markets = Table.read_table(
    'https://ucb-dsus-adopters.github.io/materials-fds-assets-v2/lectures/lec12/farmers_markets.csv')
```

Only notebooks and their `*_check.py` test helpers belong in this repo. Right
now that means 63 notebooks and 19 check scripts. Any new dataset goes to the
assets repo under a path mirroring the notebook tree.

Use that host and no other. GitHub Pages returns
`Access-Control-Allow-Origin: *`, which the browser kernel needs. Reading
straight from `inferentialthinking.com` fails with a NetworkError because it
sends no such header, and that is what broke lec01.

## What .nblink does

Every package needs a WebAssembly build. `.nblink/` declares them.

| file | purpose |
| --- | --- |
| `environment.yml` | conda environment, solved in the browser by mambajs |
| `nblink-lock.json` | pinned versions so students skip the solve |
| `jupyter-lite.json` | app name |

Two things in `environment.yml` break easily.

Channel order matters. `emscripten-forge` must be listed before `conda-forge`.
Reverse them and strict channel priority resolves `pyyaml` to the conda-forge
build, which wants the `yaml` C library that has no wasm build, and
`otter-grader-base` becomes unsolvable.

Do not pin numpy to 2.2.x. That wasm package ships
`numpy/_core/tests/_natype.py` but no `__init__.py` beside it, so
`numpy/testing/_private/utils.py` dies on
`from numpy._core.tests._natype import pd_NA`, taking
`from datascience import *` down with it. numpy 2.4 and later dropped the
import.

## Editing the environment

Change `.nblink/environment.yml`, then rebuild the lock:

```bash
pip install mambajs
mambajs create-lock .nblink/environment.yml .nblink/nblink-lock.json
```

Commit both files and push. Notebook.link reads from GitHub, so nothing changes
until you do.

A clean solve proves the packages resolve, not that they import. numpy 2.2.6
solved fine and still crashed on the first cell. After pushing, open a notebook
and run it.

## Making a link

Go to the [New Link form](https://notebook.link/launcher/link), pick "From a
GitHub Repository", and give it the repo plus a commit, tag, or branch. Leave
the reference empty and it takes the latest commit on the default branch. The
optional alias field is what produces the `@user/alias` URL above; that one was
built against `main`.

## Known gap

`project/project3/project3.ipynb` imports `sentence_transformers`, which is
built on torch and has no WebAssembly build. No environment change fixes it.
Run that project on DataHub.
