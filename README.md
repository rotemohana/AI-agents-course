# AI agents course

Course exercises and Google Colab notebooks in one Git repository.

| Directory | Contents |
| --- | --- |
| [colab-demo](colab-demo/README.md) | Introductory Colab and creative-ideas notebooks |
| [langgraph/basic examples](langgraph/basic%20examples/README.md) | Standalone ticket-routing notebook |
| [langgraph/HM/EX2](langgraph/HM/EX2/README.md) | Dana's Bakery implementation, notebook, and tests |

## Run notebooks

Upload the desired `.ipynb` file to Google Colab and run its cells in order.
Each notebook is standalone; its setup cell installs any external dependencies.
See the corresponding directory's README for prerequisites and credentials.

## Run the bakery exercise from the repository root

Use Python 3.10+ with the dependencies in `langgraph/HM/EX2/requirements.txt`.

```sh
python -m pip install -r langgraph/HM/EX2/requirements.txt
python -m unittest discover -s langgraph/HM/EX2 -v
python langgraph/HM/EX2/bakery.py
python langgraph/HM/EX2/receipts.py
python langgraph/HM/EX2/make_notebook.py
```

The receipt command writes `bakery_receipts.html` in the current working
directory. The notebook generator resolves its source files relative to its own
location and writes the notebook inside the exercise directory.

Alternatively, change into `langgraph/HM/EX2` and follow its local instructions.
Sibling imports in that exercise are resolved by Python's script directory or
the test discovery start directory; no machine-specific import paths are needed.

## Repository maintenance

The workspace root owns the only `.git` directory. Keep exercises as normal
directories, rather than initializing nested repositories or submodules.
Dependency caches, virtual environments, credentials, and notebook checkpoints
are ignored. Original nested Git metadata is archived locally in the ignored
`.git-metadata-backups/` directory for recovery; it is not part of the repository.
