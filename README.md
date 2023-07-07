# BlueButtonPro Docs

## Setup/Installation
```bash
pip3 install sphinx
pip3 install sphinx-rtd-theme
pip3 install myst-parser
```

## Build Local HTML Files
```bash
cd docs && make html && cd ..
```
## Live Updates
```bash
sphinx-autobuild docs/source docs/build/html
```

## References
- Markdown Syntax (for Sphinx): https://myst-parser.readthedocs.io/en/stable/syntax/reference.html
- RST Syntax: https://www.sphinx-doc.org/en/master/usage/restructuredtext/
