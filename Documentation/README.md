How to build this document
==================================

It's very recommended that install requirements inside a virtual environment.
```bash
git clone https://github.com/embeddedboys/Pico_DM_FPC032MRA003.git
cd Pico_DM_FPC032MRA003
python -m venv .venv
source .venv/bin/activate
```

Then install requirements
```bash
pip install mkdocs-material
pip install mkdocs-static-i18n
```

test locally, open http://127.0.0.1:8000/Pico_DM_FPC032MRA003/
```bash
mkdocs serve
```

or deploy to gh-pages
```bash
mkdocs gh-deploy
```