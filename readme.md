# How to build

1. Go to evliess.github.io folder

```bash
pip install mkdocs-material -U 
cd evliess.github.io

pip install mkdocs-minify-plugin -U
mkdocs gh-deploy --config-file ../mkdocs/mkdocs.yml --remote-branch master
```