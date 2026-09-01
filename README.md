# materials-fds-no-footprint-v2

Data 8 course materials, trimmed so they run in the browser through
[Notebook.link](https://notebook.link).

- Latest commit on the default branch:
  <https://notebook.link/github/ucb-dsus-adopters/materials-fds-no-footprint-v2/>
- Short alias pointing at `main`:
  <https://notebook.link/@caa8ee2f-1d28-45d1-844d-b247720c27d9/data8>

Only notebooks and their `*_check.py` test helpers live here. The datasets sit
in [materials-fds-assets-v2](https://github.com/ucb-dsus-adopters/materials-fds-assets-v2)
and load over HTTP, which keeps this repo under the 50 MB ceiling Notebook.link
enforces.

[docs/notebook-link.md](docs/notebook-link.md) covers the split, what `.nblink/`
does, and how to change the environment without breaking the build.
