# How to build

1. Go to evliess.github.io folder

```
pip install mkdocs-material -U 
cd evliess.github.io

pip install mkdocs-minify-plugin
mkdocs gh-deploy --config-file ../mkdocs/mkdocs.yml --remote-branch master
```